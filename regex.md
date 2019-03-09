Regex
=====

Assertions
----------

### Positive Lookbehind: `(?<=regex_2)regex_1`  

The positive lookbehind (?<=) asserts regex_1 to be immediately preceded by regex_2. Lookbehind is excluded from the match (do not consume matches of regex_2), but only assert whether a match is possible or not.

| Regex Pattern     | Test String |   |
|-------------------|-------------|---|
|`(?<=[a-z])[aeiou]`| h<b>e</b>1e |   |
|`(?<=[13579])\d`   | 3<b>4</b>   | цифра с предшествующей  нечетной цифрой |

### Negative Lookbehind: `(?<!regex_2)regex_1`

The negative lookbehind (?<!) asserts regex_1 not to be immediately preceded by regex_2. Lookbehind is excluded from the match (do not consume matches of regex_2), but only assert whether a match is possible or not.

| Regex Pattern     | Test String |   |
|-------------------|-------------|---|
|`(?<![a-z])[aeiou]`| he1<b>e</b> |   |
|`(?<=[aeiou]).`   | <b>1o</b>1<b>s</b> | символ которому не предшествует гласная |
 |