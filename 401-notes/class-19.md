# Automation: Regular Expression

## Basic Patterns: Ordinary Characters
pattern = r"Cookie" \
sequence = "Cookie" \
if re.match(pattern, sequence): \
    print("Match!") \
else: print("Not a match!")

For example, \ is just a backslash when prefixed with an **r** rather than being interpreted as an escape sequence. You will see what this means with special characters. Sometimes, the syntax involves backslash-escaped characters, and to prevent these characters from being interpreted as escape sequences; you use the raw r prefix.

## Wild Card Characters: Special Characters
1. . - A period. Matches any single character except the newline character.
re.search(r'Co.k.e', 'Cookie').group() \
'Cookie'

2. ^ - A caret. Matches the start of the string. \
re.search(r'^Eat', "Eat cake!").group() \
'Eat'

3. $ - Matches the end of string. \
re.search(r'cake$', "Cake! Let's eat cake").group() \
'cake'

[abc] - Matches a or b or c. \
[a-zA-Z0-9] - Matches any letter from (a to z) or (A to Z) or (0 to 9).

4. \ - Backslash. 

(Scenario 1) This treats '\s' as an escape character, '\s' defines a space
re.search(r'Not a\sregular character', 'Not a regular character').group() \
'Not a regular character'

(Scenario 2) '\' is treated as an ordinary character, because '\r' is not a recognized escape character \
re.search(r'Just a \regular character', 'Just a \regular character').group() \
'Just a \regular character'

(Scenario 3) '\s' is escaped using an extra `\` so its interpreted as a literal string '\s' \
re.search(r'Just a \\sregular character', 'Just a \sregular character').group()
'Just a \\sregular character'

5. \w - Lowercase 'w'. Matches any single letter, digit, or underscore.
print("Lowercase w:", re.search(r'Co\wk\we', 'Cookie').group())
6. \W - Uppercase 'W'. Matches any character not part of \w (lowercase w). \
print("Uppercase W:", re.search(r'C\Wke', 'C@ke').group())
7. \s - Lowercase 's'. Matches a single whitespace character like: space, newline, tab, return. \
print("Lowercase s:", re.search(r'Eat\scake', 'Eat cake').group()) \
Lowercase s: Eat cake
8. \S - Uppercase 'S'. Matches any character not part of \s (lowercase s). \
print("Uppercase S:", re.search(r'cook\Se', "Let's eat cookie").group()) \
Uppercase S: cookie
9. \d - Lowercase d. Matches decimal digit 0-9. \
print("How many cookies do you want? ", re.search(r'\d+', '100 cookies').group()) \
How many cookies do you want?  100
10. \D - Uppercase d. Matches any character that is not a decimal digit.
11. \t - Lowercase t. Matches tab.
12. \n - Lowercase n. Matches newline.
13. \r - Lowercase r. Matches return.
14. \A - Uppercase a. Matches only at the start of the string. Works across multiple lines as well.
15. \Z - Uppercase z. Matches only at the end of the string.
16. \b - Lowercase b. Matches only the beginning or end of the word. \
print("\\b match gives: ",re.search(r'\b[A-E]ookie', 'Cookie').group())
17. **+** - Checks if the preceding character appears one or more times starting from that position. \
re.search(r'Co+kie', 'Cooookie').group() \
'Cooookie'
18. \* -  Checks if the preceding character appears zero or more times starting from that position. \
Checks for any occurrence of a or o or both in the given sequence \
re.search(r'Ca*o*kie', 'Cookie').group() \
'Cookie'
19. ? - Checks if the preceding character appears exactly zero or one time starting from that position.

{x} - Repeat exactly x number of times. \
{x,} - Repeat at least x times or more. \
{x, y} - Repeat at least x times but no more than y times.

## Grouping in Regular Expressions
statement = 'Please contact us at: support@datacamp.com' \
match = re.search(r'([\w\.-]+)@([\w\.-]+)', statement) \
if statement: \
  print("Email address:", match.group()) # The whole \ matched text
  print("Username:", match.group(1)) # The username (group 1) \
  print("Host:", match.group(2)) # The host (group 2)
Email address: support@datacamp.com \
Username: support \
Host: datacamp.com

Another way of doing the same is with the usage of <> brackets instead. This will let you create named groups. Named groups will make your code more readable. The syntax for creating named group is: (?P<name>...). \
statement = 'Please contact us at: support@datacamp.com' \
match = re.search(r'(?P<email>(?P<username>[\w\.-]+)@(?P<host>[\w\.-]+))', statement) \ 
if statement: \
  print("Email address:", match.group('email')) \
  print("Username:", match.group('username')) \
  print("Host:", match.group('host'))

## Greedy vs. Non-Greedy Matching
pattern = "cookie" \
sequence = "Cake and cookie" 

heading  = r'\<h1>TITLE</h1>'\
re.match(r'<.*>', heading).group()\
'\<h1>TITLE</h1>'

Adding ? after the qualifier makes it perform the match in a non-greedy or minimal fashion; That is, as few characters as possible will be matched. When you run <.*>, you will only get a match with \<h1>.

heading  = r'\<h1>TITLE</h1>'\
re.match(r'<.*?>', heading).group()\
'\<h1>'

## Function provided by 're'
**compile(pattern, flags=0)**

When you need to use an expression several times in a single program, using compile() to save the resulting regular expression object for reuse is more efficient than saving it as a string. 

pattern = re.compile(r"cookie") \
sequence = "Cake and cookie" \
pattern.search(sequence).group() \
'cookie' \
This is equivalent to: \
re.search(pattern, sequence).group()

**search(pattern, string, flags=0)**

**match(pattern, string, flags=0)**

The match() function checks for a match only at the beginning of the string (by default), whereas the search() function checks for a match anywhere in the string.

**findall(pattern, string, flags=0)**

Finds all the possible matches in the entire sequence and returns them as a list of strings. Each returned string represents one match.

statement = "Please contact us at: support@datacamp.com, xyz@datacamp.com"

#'addresses' is a list that stores all the possible match
addresses = re.findall(r'[\w\.-]+@[\w\.-]+', statement)
for address in addresses:
    print(address) \
support@datacamp.com \
xyz@datacamp.com

**finditer(string, [position, end_position])**
statement = "Please contact us at: support@datacamp.com, xyz@datacamp.com" 

#'addresses' is a list that stores all the possible match \
addresses = re.finditer(r'[\w\.-]+@[\w\.-]+', statement) \
for address in addresses: \
    print(address) \
<re.Match object; span=(22, 42), match='support@datacamp.com'> \
<re.Match object; span=(44, 60), match='xyz@datacamp.com'>

**sub(pattern, repl, string, count=0, flags=0)**

**subn(pattern, repl, string, count=0)** 

statement = "Please contact us at: xyz@datacamp.com" \
new_email_address = re.sub(r'([\w\.-]+)@([\w\.-]+)',\ r'support@datacamp.com', statement) \
print(new_email_address)

**split(string, [maxsplit = 0])**

This splits the strings wherever the pattern matches and returns a list. If the optional argument maxsplit is nonzero, then the maximum 'maxsplit' number of splits are performed.

statement = "Please contact us at: xyz@datacamp.com, support@datacamp.com" \
pattern = re.compile(r'[:,]') 

address = pattern.split(statement)\
print(address) \
['Please contact us at', ' xyz@datacamp.com', ' support@datacamp.com']

IGNORECASE (I) - Allows case-insensitive matches. \
DOTALL (S) - Allows . to match any character, including newline. \
MULTILINE (M) - Allows start of string (^) and end of string ($) anchor to match newlines as well. \
VERBOSE (X) - Allows you to write whitespace and comments within a regular expression to make it more readable.

statement = "Please contact us at: support@DataCamp.com, xyz@DATACAMP.com"

Using the VERBOSE flag helps understand complex regular expressions \
pattern = re.compile(r"""
[\w\.-]+ #First part
@ #Matches @ sign within email addresses
datacamp.com #Domain
""", re.X | re.I)

addresses = re.findall(pattern, statement)\                       
for address in addresses: \
    print("Address: ", address)