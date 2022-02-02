---
layout: page
permalink: /phil/style
title: Style/Aesthetics
description: Aesthetic design choices, and cohesive common syntax
---

Since Kata is not just a single programming language (rather, a set of languages and utilities), it is important to have a shared style between different projects. This page should set some clear guidelines that all Kata projects should follow.

Of course, there will ultimately be some inconsistencies or edge-cases, but those should be rare and explained within the project they occur.

## Numbers

### Booleans

Boolean values are represented by 2 values `yes` and `no`. In many languages, booleans are often represented by `1` and `0`, `true` and `false`, or something similar. Here's a table showing the corresponding values in a few different languages:

| Kata | Python | C++ | C |
|--:|--:|--:|--:|
| `yes` | `True` | `true` | `1` |
| `no` | `False` | `false` | `0` |

### Integers

Integer values are represented by their digits, typically in base 10. The valid digits are the ASCII characters `[0-9]`. This means that Unicode "digit" characters are not supported.

For example, the number zero is `0`, and the number one is `1`. One-hundred and twenty-three is `123`, and so on.

Base 2 and 16 should also be supported as integer values, but require using the `0b` (for [binary](https://en.wikipedia.org/wiki/Binary_number), base 2) or `0x` (for [hexadecimal](https://en.wikipedia.org/wiki/Hexadecimal), base 16) prefix. In those cases, the valid digits are `[0-1]` and `[0-9a-fA-F]` respectively (base 16 digits are not case-sensitive). For example, here are some different representations that should be treated equally:

| Base 10 | Base 2 | Base 16 |
|--:|--:|--:|--:|
| `0` | `0b0` | `0x0` |
| `1` | `0b1` | `0x1` |
| `5` | `0b101` | `0x5` |
| `15` | `0b1111` | `0xF` |



## Strings

[String](https://en.wikipedia.org/wiki/String_(computer_science)) constants are written in any of the following ways:

  * `"..."` (double quotes string)
  * `'...'` (single quote string)
  * `"""..."""` (triple-quoted string)
  * `'''...'''` (triple-quoted string)
 
Triple quoted strings may contain line breaks (in which case, the string value should include corresponding newline characters), whereas quotes with a single delimiter should not contain any line breaks (newlines must be entered via the escape sequence `\n`).

Further, data within the delimiters (`'` or `"`) may be valid UTF-8, ASCII, or an escape sequence. The common escape sequences are:

  * `\n`: newline
  * `\r`: carriage return
  * `\t`: tab
  * `\\`: backslash
  * `\'`: single quote
  * `\"`: double quote
  * `\xHH`: hexadecimal character code with leading zeros (e.g. `\x41` is `A`)
  * `\uHHHH`: 16-bit Unicode character code with leading zeros (e.g. `\u0041` is `A`)
  * `\UHHHHHHHH`: 32-bit Unicode character code with leading zeros (e.g. `\U00000041` is `A`)

## Names

Names, or identifiers, in Kata are allowed to be any UTF-8 identifier, with the following restrictions:

  * names may not start with a number
  * all characters in a name must be one of:
    * number/digit
    * letter
    * `_` (underscore)

Specifically, characters are considered a letter if they in the `L*` categories in [Unicode](https://en.wikipedia.org/wiki/Unicode_character_property).


For example, assuming ASCII-only, the regular expression for valid names is: `[a-zA-Z_][a-zA-Z0-9_]*`.

