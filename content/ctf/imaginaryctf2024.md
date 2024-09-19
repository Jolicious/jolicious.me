---
title: "Writeup of Imaginary CTF 2024"
date: 2024-07-25T07:07:07+01:00
description: "By Jolicious"
tags: ["CTF", "ImaginaryCTF", "2024ctf", "writeup"]
type: post
weight: 25
showTableOfContents: true
---

## Web Challenge

### Crystals (100pts)

![Untitled](/imaginaryctf/Crystals1.png)

We view the Webpage, seem like a normal static page.

![Untitled](/imaginaryctf/Crystals2.png)

Let’s look at the source code.

`app.rb`

```ruby
require 'sinatra'

# Route for the index page
get '/' do
  erb :index
end
```

It just routes us to the index page if we visit `/` .

Now let’s look at the file `docker-compose.yml`

```yaml
version: '3.3'
services:
  deployment:
    hostname: $FLAG
    build: .
    ports:
      - 10001:80

```

We can see that the flag is in the hostname.

Found an article regarding hostname!!

[https://www.geeksforgeeks.org/hostname-in-docker/](https://www.geeksforgeeks.org/hostname-in-docker/)

Seems like the hostname cannot be access from anywhere in the page.

Unless if we can crash the page which would give errors containing information like hostname, server information.

But what kind of status code would give information such as that?

Got an image while researching about it which gave us information such as hostname, port, server used.

![Untitled](/imaginaryctf/Crystals3.png)

Lets send some invalid characters which the server will not be able to process.

Characters 

```js
` , { , } , ^ , [ , ] , | , \ , " , < , > , 
```

When we send the from the browser, we can see that the characters gets url encoded, making the server able to understand the request making the motive fail.

![Untitled](/imaginaryctf/Crystals4.png)

So we can then intercept the request in Burpsuite and send it raw without url encoding.

![Untitled](/imaginaryctf/Crystals5.png)

Hence we are able to retrieve the flag.

```xml
Flag: ictf{seems_like_you_broke_it_pretty_bad_76a87694}
```

### Readme (100 points)

![Untitled](/imaginaryctf/Readme1.png)

We view the Webpage, seem like a nothing is there beside the text `It Works!`.

![Untitled](/imaginaryctf/Readme2.png)

Let’s look at the source code to see if we can get some information from there.

`app.js`

```jsx
const express = require('express')
const path = require('path')

const app = express()
app.use(express.static(path.join(__dirname, '../public')))
app.listen(8000)
```

Nothing interesting here,  let’s look at other files.

`docker-compose.yml`

```jsx
services:
  web:
    image: readme
    build: .
    ports:
      - "8000:80"
    environment:
      - FLAG=flag{test_flag}
```

There is a test flag in the environment variable.

`Dockerfile`

```php  
FROM node:20-bookworm-slim

RUN apt-get update \
    && apt-get install -y nginx tini \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

WORKDIR /app
COPY package.json yarn.lock ./
RUN yarn install --frozen-lockfile
COPY src ./src
COPY public ./public

COPY default.conf /etc/nginx/sites-available/default
COPY start.sh /start.sh

ENV FLAG="ictf{path_normalization_to_the_rescue}"

ENTRYPOINT ["/usr/bin/tini", "--"]
CMD ["/start.sh"]
```

And there we go we got the flag.

```jsx
Flag: ictf{path_normalization_to_the_rescue}
```

### Journal (100 points)

![Untitled](/imaginaryctf/Journal1.png)

Looking at the Webpage, we can see there are 5 files.

![Untitled](/imaginaryctf/Journal2.png)

When we click on one of file, we can see a journal written there with a parameter `file` .

![Untitled](/imaginaryctf/Journal3.png)

Let’s try to get flag.txt by supplying `..` , we get the following error.

![Untitled](/imaginaryctf/Journal4.png)

Let’s look at the source code.

`Dockerfile`

```php
FROM php:7-apache

RUN /usr/sbin/useradd -u 1000 user

COPY index.php /var/www/html/
RUN chown -R www-data:www-data /var/www/html && \
    chmod -R 444 /var/www/html && \
    chmod 555 /var/www/html

COPY flag.txt /flag.txt
COPY files /var/www/html/files/
RUN mv /flag.txt /flag-`tr -dc A-Za-z0-9 < /dev/urandom | head -c 20`.txt

VOLUME /var/log/apache2
VOLUME /var/run/apache2

CMD bash -c 'source /etc/apache2/envvars && APACHE_RUN_USER=user APACHE_RUN_GROUP=user /usr/sbin/apache2 -D FOREGROUND'
```

We can see that the `flag.txt` file is at the root directory (`/`).

```php
<?php

echo "<p>Welcome to my journal app!</p>";
echo "<p><a href=/?file=file1.txt>file1.txt</a></p>";
echo "<p><a href=/?file=file2.txt>file2.txt</a></p>";
echo "<p><a href=/?file=file3.txt>file3.txt</a></p>";
echo "<p><a href=/?file=file4.txt>file4.txt</a></p>";
echo "<p><a href=/?file=file5.txt>file5.txt</a></p>";
echo "<p>";

if (isset($_GET['file'])) {
  $file = $_GET['file'];
  $filepath = './files/' . $file;

  assert("strpos('$file', '..') === false") or die("Invalid file!");

  if (file_exists($filepath)) {
    include($filepath);
  } else {
    echo 'File not found!';
  }
}

echo "</p>";
```

We can the that `strpos()` function is blocking `..` . Looks like there is nothing here.

There is another method we can try that is encoding, but it won’t work so I wont go there.

The last option we got is to check if we have any exploit regarding the function used.

We got an article from HackTricks regarding this and got similar code used.

[https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/php-tricks-esp#rce-via-assert](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/php-tricks-esp#rce-via-assert)

We can get rce(Remote Code Execution).

![Untitled](/imaginaryctf/Journal5.png)

In the article, we see that is is being used in the `page` parameter, we have to use it in `file` parameter.

![Untitled](/imaginaryctf/Journal6.png)

And there we go, we have command execution. The name of flag.txt is different than usual.

Let’s keep that filename and get the flag.

![Untitled](/imaginaryctf/Journal7.png)

```php
Flag: ictf{assertion_failed_e3106922feb13b10}
```