# 📑 STRling Documentation

### [⇦ Back to Project Overview](../README.md)

## 😅 Don't worry if you can't remember it all!

We have well structured and explanatory docustrings for each function
that allow you to understand exactly how it works by just hovering your mouse.

```python
from STRling import simply as s
import re

####################
# Range Feature
####################

# Most methods allow the `min_rep` and `max_rep` to be passed in directly or after params.
# For example, simply.letter(1, 3) will match 1 to 3 letters.

# But some methods take an unknown number of parameters and can't distinguish the range.
# For example, s.merge(simply.letter(), simply.digit(), 1, 2). <==== INVALID

# However, all methods allow setting the range by externally invoking unless otherwise specified.
# For example, simply.letter()(1, 2) is the same as simply.letter(1, 2).

# This external invocation may seem useless, but it can solve our earlier issue.
# For example, s.merge(simply.letter(), simply.digit())(1, 2). <==== VALID

# Notice for all functions (where repetition is valid) we can invoke the range outside the parameters,
# but it is primarily useful for functions with an unknown number of parameters.

####################
# Custom Literals
####################

# Creates a matching pattern from a regular string
s.lit('$%')  # Matches the literal characters '$' or '%'.

####################
# Character Sets
####################

# Note: Each character set below has a negated counterpart.
# For example, simply.letter() => simply.not_letter().

s.alpha_num()    # Matches any letter (uppercase or lowercase) or digit.
s.special_char() # Matches any special character. => !"#$%&'()*+,-./:;<=>?@[\]^_`{|}~

s.letter()       # Matches any letter (uppercase or lowercase).
s.upper()        # Matches any uppercase letter.
s.lower()        # Matches any lowercase letter.

s.digit()        # Matches any digit.
s.hex_digit()    # Matches any letter A through F (uppercase or lowercase) or a digit (0-9).

s.whitespace()   # Matches any whitespace character (space, tab, newline, carriage return, etc.).
s.newline()      # Matches a newline character.
s.bound()        # Matches a boundary character.

# Note: Each character set below doesn't has a negated counterpart.
# For example, simply.tab(), but there is no simply.not_tab().

s.tab()          # Matches a tab character.
s.carriage()     # Matches a carriage return character.

####################
# Anchors
####################

s.start()  # Matches the start of a line.
# There is no `simply.not_start()` function.
# Instead, use `simply.not_behind(simply.start())`.

s.end()  # Matches the end of a line.
# There is no `simply.not_end()` function.
# Instead, use `simply.not_ahead(simply.end())`.

####################
# Custom Sets
####################

# Note: Each custom set below has a negated counterpart.
# For example, simply.between() => simply.not_between()

# Matches all characters within and including the start and end of a letter or number range.
s.between(0, 9)      # Matches any digit from 0 to 9.
s.between('a', 'z')  # Matches any lowercase letter from 'a' to 'z'.
s.between('A', 'Z')  # Matches any uppercase letter from 'A' to 'Z'.

# Matches any provided patterns, but they can't include subpatterns.
s.in_chars(s.letter(), s.digit(), ',.')  # Matches any letter, digit, comma, and period.
# A composite pattern is one consisting of subpatterns (created by constructors and lookarounds).

####################
# Constructors
####################

s.any_of()  # Matches any pattern, including patterns consisting of subpatterns.
# A composite pattern is one consisting of subpatterns (created by constructors and lookarounds).
pattern1 = s.merge(s.digit(3), s.letter(3))  # Matches 3 digits followed by 3 letters.
pattern2 = s.merge(s.letter(3), s.digit(3))  # Matches 3 letters followed by 3 digits.
s.any_of(pattern1, pattern2)  # Matches either pattern1 or pattern2


s.may()  # Optionally matches the provided patterns.
# If a `may` pattern isn't there, it still will match the rest of the patterns.
s.merge(s.letter(), s.may(s.digit()))
# Matches any letter, and includes any digit following the letter.
# In the text, "AB2" the pattern above matches 'A' and 'B2'.


s.merge()  # Combines multiple patterns into one larger pattern.
# You can see this used for the method above.


s.capture()  # Creates a numbered group that can be indexed for extracting part of a match later.
# Capture is used the same as merge.
s.capture(s.letter(), s.digit())

# Captures CANNOT be invoked with a range: s.capture(s.digit(), s.letter())(1, 2) <== INVALID
# Captures CAN be invoked with a number of copies: s.capture(s.digit(), s.letter())(3) <== VALID

three_digit_group = s.capture(s.digit(3))
four_groups_of_three = three_digit_groups(4)

example_text = "Here is a number: 111222333444"
match = re.search(str(four_groups_of_three), example_text)  # Notice str(pattern)

print("Full Match:", match.group())
print("First:", match.group(1))
print("Second:", match.group(2))
print("Third:", match.group(3))
print("Fourth:", match.group(4))

# Output:
# Full Match: 111222333444
# First: 111
# Second: 222
# Third: 333
# Fourth: 444


s.group()  # Creates a named group that can be referenced for extracting part of a match.
# group is used the same as merge and capture but it takes a string name as the first argument.
s.group('my_group', s.letter(), s.digit())

# Unlike merge and capture, groups CANNOT be invoked with a range.
# This is because group names must be unique in a pattern.
# s.group('unique_name', s.digit())(1, 2) <== INVALID

# Groups can easily be referenced from the match by name
# assuming the numbers have been grouped and named properly.
example_text = "Here is a phone number: 123-456-7890."
match = re.search(str(phone_number_pattern), example_text)  # Notice str(pattern)

print("Full Match:", match.group())
print("Area Code:", match.group("area_code"))
print("Central Part:", match.group("central_part"))
print("Last Part:", match.group("last_part"))

# Output:
# Full Match: 123-456-7890
# Area Code: 123
# Central Part: 456
# Last Part: 7890


####################
# Lookarounds
####################

# Note: Each lookaround below has a negated counterpart.
# For example, simply.ahead() => simply.not_ahead().

# These verify a pattern is or isn't ahead or behind
# without capturing it as part of the pattern matched.

s.ahead()  # Only matches the rest of a pattern if the provided pattern is ahead.
# For example, in the text "123ABC", the pattern below matches 3 but not 1 or 2.
s.merge(s.digit(), s.ahead(s.letter()))  # Only matches a digit followed by a letter.


s.behind()  # Only matches the rest of a pattern if the provided pattern is behind.
# For example, in the text "123ABC", the pattern below matches A but not B or C.
s.merge(s.behind(s.digit()), s.letter())  # Only matches a letter preceded by a digit.


s.has()  # Only matches the rest of a pattern if the provided pattern is not within.
# For example, in the text "123ABC", the pattern below will only match because it contains a digit.
s.has(s.digit())  # Only matches a because it contains a digit.
# Note, the negated counterpart of simply.has() is simply.has_not().
```

Simplify your string validation and matching tasks with STRling, the all-in-one solution for developers who need a powerful yet user-friendly tool for working with strings. No longer write RegEx using complex jargon or the various syntaxes string validation specific to independent libraries. Download and start using STRling today!
