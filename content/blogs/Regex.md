---
title: "Regular Expression (Regex) for all"
date: 2024-09-18T17:55:28+08:00
description: "By Jolicious"
tags: ["regex", "regular expression", "python"]
type: post
weight: 25
showTableOfContents: true
---


## Code 1
```py
import re

def matchregex(pattern, text):
    match = re.findall(pattern, text)
    if match:
        print("Found:", ''.join(match))
    else:
        print("No match found")
```

In simple this function will take a pattern and a text if the text matches the pattern it print the matched text. If not then it will print __'No match found'__.

Some basics:

## Quantifiers (Repetition):
### Asterisk `*`
The asterisk (*) in regular expressions is a quantifier that tells the pattern to look for only one character or one group (group is defined through brackets such as `(abc)` where `abc` is a group) right before it, and match it as many times as possible, __*including zero times.*__

For eg:
```python
matchregex(r'ab*c', 'abbbc')
"Found: abbbc"
```
![alt text](/Regex/regex1.png)

```py
matchregex(r'ab*c', 'aaabbbc')
"Found: abbbc"
```

```py
matchregex(r'(ab)*c', 'ababc')
"Found: ab"
```
**Q.** Wait but why didn't the other **'a'** print?

**->** It is looking for a pattern such as **'abc'** in which **'b'** can be repeated 0 times or any number of times because it is before the asterisk (*). It is matching the pattern **'abc'** from 3rd **'a'**, so it in printing from there.

```py
matchregex(r'abc*d', 'abbbcd')
"No match found"
```
**Q.** Why didn't it show any matches?

**->** It is looking for a pattern such as **'abcd'** in which **'c'** can be repeated 0 times or any number of times. It is not matching because **'c'** can be repeated multiple times, but since **'b'** is repeated multiple times, it breaks the pattern.

### Plus `+`
The (+) symbol in regular expressions is a quantifier that tells the pattern to look for the character or group right before it, and match it as many times as possible, ***but not including zero times.***

For eg:
```py
matchregex(r'abc+','ab')
"No match found"
```
Because there is no occurance of __c__, it prints "No match found".

Difference between __*__ and __+__ is below:

```py
matchregex(r'abc*','ab')
"Found: ab"
```

```py
matchregex(r'abc+','ab')
"No match found"
```

### Question Mark `?`
The question mark (?) in regular expressions is a quantifier that tells the pattern to look for only one character or one group right before it, and match it only 1 time or __*include zero times.*__

For eg:
```py
matchregex(r'https?://','https://')
"Found: https://"
```

```py
matchregex(r'https?://','http://')
"Found: http://"
```

```py
matchregex(r'https?://','httpss://')
"No match found"
```

### Exact Count `{n}`
It specifies that the preceding character or group must appear exactly n times in the string to make a match.

For eg:
```py
matchregex(r'c{5}','ccccc')
"Found: ccccc"
```

```py
matchregex(r'abc{5}','abbccccc')
"No match found"
```

```py
matchregex(r'abc{5}','abccccc')
"Found: abccccc"
```

It printed "No match found when "b" is provided twice in the second case because, it is trying to match a pattern "abc" where "c" can be repeated 5 number of times.

### Minimum Count `{n,}`
The preceding character or group must appear at least n times, but there’s no upper limit. It can match n, n+1, n+2, and so on.

For eg:
```py
matchregex(r'abc{5,}','abcc')
"No match found"
```

```py
matchregex(r'abc{5,}','abccccc')
"Found: abccccc"
```

```py
matchregex(r'abc{5,}','abcccccccc')
"Found: abcccccccc"
```

### Range Count `{n,m}`
The preceding character or group must appear at least n times and at most m times.

For eg:
```py
matchregex(r'c{5,6}','cccccc') # 6 'c' characters
"Found: cccccc"
```

```py
matchregex(r'c{5,6}','ccccc') # 5 'c' characters
"Found: ccccc"
```

```py
matchregex(r'c{5,6}','cccc')
"No match found"
```

## Anchors (Positioning):
### Start of Line `^`
It ensures that the match happens at the beginning of a string. The pattern must appear right at the start of the string. If it occurs elsewhere, the match will fail.

For eg:
```py
matchregex(r'^c','chatbot')
"Found: c"
```
### End of Line `$`
It ensures that the match happens at the end of a string. The pattern must appear left at the end of the string. If it occurs earlier, the match will fail.

```py
matchregex(r't$','chatbot')
"Found: t"
```

## Character Classes (Sets):
### Dot `.`
The `.` (dot) in regex is a wildcard that matches any single character except a newline (`\n`).

For eg:
```py
matchregex(r'c.t','cat')
"Found: cat"
```

```py
matchregex(r'c.t','c-t')
"Found: c-t"
```

```py
matchregex(r'c.t','caat')
"No match found"
```

### Character Class `[...]`
It matches exactly one character at a time.

For eg:
```py
matchregex(r'[aeiou]','heello')
"Found: eeo"
```

```py
matchregex(r'[aeiou]','hello')
"Found: eo"
```

```py
matchregex(r'[a-z]','hello')
"Found: hello"
```

```py
matchregex(r'[a-z]','Hello')
"Found: ello"
```

```py
matchregex(r'[a-zA-Z0-9]','Hello123')
"Found: Hello123"
```

### Negated Character Class `[^...]`
The regex pattern [^...] is used to match any character that is not included in the specified set.

For eg:
```py
matchregex(r'[^a]','ameow')
"Found: meow"
```

```py
matchregex(r'[^a]','maeow')
"Found: meow"
```

## Predefined Character Classes:
### Digit `\d`
The regex pattern \d is used to match any single digit character from 0 to 9. excluding spaces

For eg:
```py
matchregex(r'\d','126748 99')
"Found: 12674899"
```

### Non-Digit `\D`
The regex pattern \D is used to match any character that is not a digit including spaces. 

For eg:
```py
matchregex(r'\D','126748hello hi')
"Found: hello hi"
```

### Word Character `\w`
The regex pattern \w is used to match any "word character," which includes alphanumeric characters (letters and digits) and the underscore (_) excluding spaces.

For eg:
```py
matchregex(r'\w','126748hello hi')
"Found: 126748hellohi"
```

### Non-Word Character `\W`
The regex pattern \W is used to match any character that is not a word character including spaces. 

For eg:
```py
matchregex(r'\W','12 !@#$ $%^&')
"Found:  !@#$ $%^&"
```
### Whitespace Character `\s`
The regex pattern \s is used to match any whitespace character, including spaces, tabs, newlines, and other whitespace characters.

For eg:
```py
matchregex(r'\s','126748\t99')
"Found: "
```
It showed empty result as spaces cannot be seen.

### Non-Whitespace Character `\S`
The regex pattern \S is used to match any character that is not a whitespace character.

For eg:
```py
matchregex(r'\S','126748\t99')
"Found: 12674899"
```

## Grouping and Alternation
### Capturing Group `(...)`
Groups multiple elements together.

For eg:
```py
matchregex(r'(abc)','abc abc abc bc')
"Found: abcabcabc"
```

### Alternation `|`
Alternation (acts like OR). Matches the pattern before or after the |.

For eg:
```py
matchregex(r'cat|bat|nat','cat rhymes with bat, right?')
"Found: catbat"
```

## Escape Sequences
### Backslash `\\`
Escapes special characters (e.g., \*, \+, \.). Allows you to use characters literally.

## Boundary Matchers
### Word Boundary `\b`
Finds the spot where a word starts or ends and it shouldn't be surrounded by characters. 

For eg:
```py
matchregex(r'\bbat\b','batact')
"No match found"
```

```py
matchregex(r'\bbat','catbatact')
"No match found"
```

```py
matchregex(r'\bbat','cat bat act')
"Found: bat"
```

```py
matchregex(r'\bbat','batact')
"Found: bat"
```

#### DIfference between ^$ and \b

```py
matchregex(r'^bat$','cat bat act')
"No match found"
```

```py
matchregex(r'\bbat\b','cat bat act')
"Found: bat"
```

### Non-Word Boundary `\B`
Matches a non-word boundary which mean the word should be surrounded by random characters.

For eg:
```py
matchregex(r'\Bbat\B','catbatact')
"Found: bat"
```

## Regex Special Constructs
### Atomic Group `(?>...)`
An atomic group is a special type of group in regular expressions that helps the pattern engine by preventing it from reconsidering or backtracking within that group once it has made a match.

In simpler terms with examples:
```py
matchregex(r"(?>a|ab)c","abc")
"No match found"
```
The regex first tries to match **'a'** followed by **'c'**, if it fails it will not try again to match **'ab'** followed by **'c'**.

### Positive Lookahead `(?=...)`
Checks if a pattern exists after the current position but does not include it in the match.

In simpler terms with examples:
```py
matchregex(r"\d+(?=\D)","123abc")
"Found: 123"
```
In the above result, the regex checks if any alphabets are there after digits, if there is, it matches, only the digits will print and the alphabets will be ignored.

### Negative Lookahead `(?!...)`
Checks if a pattern does not exist after the current position.

In simpler terms with examples:
```py
matchregex(r"\d+(?!\D)","3abc")
"No match found"
```

```py
matchregex(r"\d+(?!\D)","3")
"Found: 3"
```
In the above result, the regex checks if no alphabets are there after digits. If there is no alphabets, it will match else it will fail.

### Positive Lookbehind `(?<=...)`
Ensures that a specific pattern precedes the current position.

In simpler terms with examples:
```py
matchregex(r"(?<=\$)\d+","$300, 400, $500")
"Found: 300500"
```
In the above result, the regex checks if the **'$'** is before the digits, if it is, it will match else it will fail. Since **'$'** is before digits of **'300'** and **'500'**, it matches them.

### Negative Lookbehind `(?<!...)`
Ensures that a specific pattern does not precede the current position.

In simpler terms with examples:
```py
matchregex(r"(?<!\$)\d*","$3, 4, $5")
"Found: 4"
```
In the above result, the regex checks if the **'$'** is not before the digits, if it matches then it will print it. Since **'4'** is not after the **'$'**, it matches.

### Named Capture Groups `(?P<name>...)`
A named capture group allows you to assign a name to a capturing group, which can be useful when you want to refer to specific groups later in the pattern or when processing matches.

## Code 2
The code to be used for the following example is:
```py
import re

def matchregex1(pattern, text):
    match = re.search(pattern, text)
    if match:
        print("Found:", match.group('year'))
    else:
        print("No match found")
```

In simpler terms with examples:
```py
matchregex1(r"(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})", "Today's date is 2024-09-19")
"Found: 2024"
```

Explanation:
- `(?P\<year\>\d{4})`: Captures the year as a named group year.
- `(?P\<month\>\d{2})`: Captures the month as a named group month.
- `(?P\<day\>\d{2})`: Captures the day as a named group day.

### Non-Capturing Group `(?:...)`
A non-capturing group ((?:...)) is used to group parts of a regex together without saving (or capturing) that part separately. It still matches the text, but it won't save it as a separate part for later use.

For eg:

```py
matchregex(r"(?:http|https)://\w+\.\w+", "Visit our site at https://example.com and http://mysite.com")
"Found: https://example.comhttp://mysite.com"
```

```py
matchregex(r"(http|https)://\w+\.\w+", "Visit our site at https://example.com and http://mysite.com")
"Found: httpshttp"
```

In the first example, even though http or https is matched, it's not saved as a separate group. But the whole URL (`https://example.com` or `http://mysite.com`) will still be matched as part of the entire pattern.

In the second example without Non-Capturing Group, http or https is matched with the rest of the url, only https and http are printed instead of the whole url.

## Code 3
```py
def matchregex2(pattern, text):
    match = re.search(pattern, text)
    if match:
        print("Found:", match.group())
    else:
        print("No match found")
```

### Conditional Groups `(?(condition)yes-pattern|no-pattern)`
- `condition`: A condition that determines which pattern to match.
- `yes-pattern`: The pattern to match if the condition is true.
- `no-pattern`: The pattern to match if the condition is false (optional).

For eg:
```py
matchregex2(r"(\d{4})-(?(1)\d{2}-\d{2}|(\d{2}-\d{2}))", "Dates: 2024-09-19 and 09-19")
"Found: 2024-09-19"
```
Simple Explanation:
- `(?(1)`: The regex checks if the first group (the year) is present.
- `\d{2}-\d{2}`: If it is, it expects the rest of the pattern to be in the `YYYY-MM-DD` format.
- `(\d{2}-\d{2})`: If the year is not present, it matches the `MM-DD` format directly.