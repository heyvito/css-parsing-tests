# CSS parsing tests


This repository contains implementation-independent test for CSS parsers, based
on the 2025 Editor's Draft of the [`CSS Syntax Level 3`](https://drafts.csswg.org/css-syntax/)
specification.

The upstream repository for these tests is at
[CourtBouillon/css-parsing-tests](https://github.com/CourtBouillon/css-parsing-tests)


## Projects using this


CSS parsers using these tests:

* [`tinycss`](https://github.com/heyvito/tinycss) (Ruby)


## Importing


The recommended way to use these tests in an implementation is to import them
with [git-subtree](https://github.com/git/git/tree/master/contrib/subtree).

To import the first time to a `./css-parsing-tests` sub-directory, run this from
the top-level of a git repository:

```
git subtree add -P css-parsing-tests https://github.com/heyvito/css-parsing-tests.git main
```

Later, to merge changes made in the upstream repository, run:

```
git subtree pull -P css-parsing-tests https://github.com/heyvito/css-parsing-tests.git main
```


## Test files


CSS Syntax specification describes a number of "functions".
Each `.json` file in this repository corresponds to such a function.
The files are encoded as UTF-8 and each contain a JSON array with an even
number of items, where each pair of items is one function input associated
with the expected result.

### `component_value_list.json`

Tests [_Parse a list of component values_](https://drafts.csswg.org/css-syntax-3/#parse-a-list-of-component-values)
The Unicode input is represented by a JSON string,
the output as an array of `component values` as described below.

### `component_value_list.json`

Tests [_Parse a component value_](https://drafts.csswg.org/css-syntax-3/#parse-a-component-value).
The Unicode input is represented by a JSON string, the output as a
`component value`.

### `declaration_list.json`

Tests [_Parse a list of declarations_](https://drafts.csswg.org/css-syntax-3/#parse-a-list-of-declarations>).
The Unicode input is represented by a JSON string,
the output as an array of `declarations` and `at-rules`.

### `blocks_contents.json`

Tests [_Parse a block’s contents_](http://dev.w3.org/csswg/css-syntax-3/#parse-block-contents>).
The Unicode input is represented by a JSON string,
the output as an array of `declarations`, `at-rules` and `qualified rules`.

### `one_declaration.json`

Tests [_Parse a declaration_](https://drafts.csswg.org/css-syntax-3/#parse-a-declaration>).
The Unicode input is represented by a JSON string, the output as a
`declaration`.

### `one_rule.json`

Tests [_Parse a rule_](https://drafts.csswg.org/css-syntax-3/#parse-a-rule).
The Unicode input is represented by a JSON string, the output as a
`qualified rule` or `at-rule`.

### `rule_list.json`

Tests [_Parse a list of rules_](https://drafts.csswg.org/css-syntax-3/#parse-a-list-of-rules>).
The Unicode input is represented by a JSON string, the output as a list of
`qualified rules` or `at-rules`.

### `stylesheet.json`

Tests [_Parse a stylesheet_](https://drafts.csswg.org/css-syntax-3/#parse-a-stylesheet).
The Unicode input is represented by a JSON string, the output as a list of
`qualified rules` or `at-rules`.

### `stylesheet_bytes.json`

Tests [_Parse a stylesheet_](https://drafts.csswg.org/css-syntax-3/#parse-a-stylesheet)
together with [_The input byte stream)](https://drafts.csswg.org/css-syntax-3/#input-byte-stream).
The input is represented as a JSON object containing:

* A required `css_bytes`, the input byte string, represented as a JSON string
where code points U+0000 to U+00FF represent bytes of the same value.
* An optional `protocol_encoding`, a protocol encoding label as a JSON string,
or null.
* An optional `environment_encoding`, an environment encoding label as a JSON
string, or null.
* An optional `comment` that is ignored.

The output is represented a list of `qualified rules` or `at-rules`.

### `color_*.json`

Tests the `<color>` syntax defined in both in
[_CSS Color Level 3_](https://www.w3.org/TR/css-color-3/#colorunits) and
[_CSS Color Level 4_](https://www.w3.org/TR/css-color-4/#colorunits).
The Unicode input is represented by a JSON string, the output as one of:

* null if the input is not a valid color in CSS syntax, or
* a string corresponding to the serialization of the result, as defined in
level 4.

### `An+B.json`

Tests the [_An+B_](https://drafts.csswg.org/css-syntax-3/#the-anb-type>) syntax
defined in CSS Syntax Level 3.
This [differs](https://drafts.csswg.org/css-syntax-3/#changes) from the
[nth grammar rule](https://www.w3.org/TR/selectors-3/#nth-child-pseudo) in
Selectors Level 3 only in that `-` characters and digits can be escaped in
some cases.
The Unicode input is represented by a JSON string, the output as null for
invalid syntax, or an array of two integers `[A, B]`.


## Result Representation

AST nodes (the results of parsing) are represented in JSON as follow.
This representation was chosen to be compact (and thus less annoying to write
by hand) while staying unambiguous.
For example, the difference between `@import` and `\@import` is not lost:
they are represented as `["at-keyword", "import"]` and `["ident", "@import"]`,
respectively.


### Rules and declarations


#### At-rule

An array of length 4: the string `"at-rule"`,
the name (value of the at-keyword) as a string,
the prelude as a nested array of `component values`,
and the optional block as a nested array of component value, or null.

#### Qualified rule

An array of length 3: the string `"qualified rule"`,
the prelude as a nested array of `component values`,
and the block as a nested array of component value.

#### Declaration

An array of length 4: the string `"declaration"`, the name as a string,
the value as a nested array of `component values`,
and a the important flag as a boolean.

### Component values

#### `<ident>`

Array of length 2: the string `"ident"`, and the value as a string.

#### `<at-keyword>`

Array of length 2: the string `"at-keyword"`, and the value as a string.

#### `<hash>`

Array of length 3: the string `"hash"`, the value as a string,
and the type as the string `"id"` or `"unrestricted"`.

#### `<string>`

Array of length 2: the string `"string"`, and the value as a string.

#### `<bad-string>`

Array of length 1: the string `"bad-string"`.

#### `<url>`

Array of length 2: the string `"url"`, and the value as a string.

#### `<bad-url>`

Array of length 1: the string `"bad-url"`.

#### `<delim>`

The value as a one-character string.

#### `<number>`

Array of length 4: the string `"number"`, the representation as a string,
the value as a number, and the type as the string `"integer"` or `"number"`.

#### `<percentage>`

Array of length 4: the string `"percentage"`, the representation as a string,
the value as a number, and the type as the string `"integer"` or `"number"`.

#### `<dimension>`

Array of length 4: the string `"dimension"`, the representation as a string,
the value as a number, the type as the string `"integer"` or `"number"`,
and the unit as a string.

#### `<unicode-range>`

Array of length 3: the string `"unicode-range"`,
followed by the *start* and *end* integers as two numbers.

#### `<include-match>`

The string `"~="`.

#### `<dash-match>`

The string `"|="`.

#### `<prefix-match>`

The string `"^="`.

#### `<suffix-match>`

The string `"$="`.

#### `<substring-match>`

The string `"*="`.

#### `<column>`

The string `"||"`.

#### `<whitespace>`

The string `" "` (a single space.)

#### `<CDO>`

The string `"<!--"`.

#### `<CDC>`

The string `"-->"`.

#### `<colon>`

The string `":"`.

#### `<semicolon>`

The string `";"`.

#### `<comma>`

The string `","`.

#### `{}` block

An array of length N+1: the string `"{}"`
followed by the N `component values` of the block’s content.

#### `[]` block

An array of length N+1: the string `"[]"`
followed by the N `component values` of the block’s content.

#### `()` block

An array of length N+1: the string `"()"`
followed by the N `component values` of the block’s content.

#### Function

An array of length N+2: the string `"function"`
and the name of the function as a string
followed by the N `component values` of the function’s arguments.

#### `<bad-string>`

The array of two strings `["error", "bad-string"]`.

#### `<bad-url>`

The array of two strings `["error", "bad-url"]`.

#### Unmatched `<}>`

The array of two strings `["error", "}"]`.

#### Unmatched `<]>`

The array of two strings `["error", "]"]`.

#### Unmatched `<)>`

The array of two strings `["error", ")"]`.
