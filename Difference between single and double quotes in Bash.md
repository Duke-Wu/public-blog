## [Difference between single and double quotes in Bash](https://stackoverflow.com/questions/6697753/difference-between-single-and-double-quotes-in-bash)

In Bash, what are the differences between single quotes (`''`) and double quotes (`""`)?

## Answers 01

Single quotes won't interpolate anything, but double quotes will. For example: variables, backticks, certain `\` escapes, etc.

Example:

```sh
$ echo "$(echo "upg")"
upg
$ echo '$(echo "upg")'
$(echo "upg")
```

The Bash manual has this to say:

> [3.1.2.2 Single Quotes](http://www.gnu.org/software/bash/manual/html_node/Single-Quotes.html)
>
> Enclosing characters in single quotes (`'`) preserves the literal value of each character within the quotes. A single quote may not occur between single quotes, even when preceded by a backslash.
>
> [3.1.2.3 Double Quotes](http://www.gnu.org/software/bash/manual/html_node/Double-Quotes.html)
>
> Enclosing characters in double quotes (`"`) preserves the literal value of all characters within the quotes, with the exception of `$`, ```, `\`, and, when history expansion is enabled, `!`. The characters `$` and ``` retain their special meaning within double quotes (see [Shell Expansions](https://www.gnu.org/software/bash/manual/html_node/Shell-Expansions.html#Shell-Expansions)). The backslash retains its special meaning only when followed by one of the following characters: `$`, ```, `"`, `\`, or newline. Within double quotes, backslashes that are followed by one of these characters are removed. Backslashes preceding characters without a special meaning are left unmodified. A double quote may be quoted within double quotes by preceding it with a backslash. If enabled, history expansion will be performed unless an `!` appearing in double quotes is escaped using a backslash. The backslash preceding the `!` is not removed.
>
> The special parameters `*` and `@` have special meaning when in double quotes (see [Shell Parameter Expansion](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html#Shell-Parameter-Expansion)).



## Answers 02

If you're referring to what happens when you echo something, the single quotes will literally echo what you have between them, while the double quotes will evaluate variables between them and output the value of the variable.

For example, this

```sh
#!/bin/sh
MYVAR=sometext
echo "double quotes gives you $MYVAR"
echo 'single quotes gives you $MYVAR'
```

will give this:

```sh
double quotes gives you sometext
single quotes gives you $MYVAR
```



## Answers 03

The [accepted answer](https://stackoverflow.com/a/6697781/6862601) is great. I am making a table that helps in quick comprehension of the topic. The explanation involves a simple variable `a` as well as an indexed array `arr`.

If we set

```sh
a=apple      # a simple variable
arr=(apple)  # an indexed array with a single element
```

and then `echo` the expression in the second column, we would get the result / behavior shown in the third column. The fourth column explains the behavior.

```none
 # | Expression  | Result      | Comments
---+-------------+-------------+--------------------------------------------------------------------
 1 | "$a"        | apple       | variables are expanded inside ""
 2 | '$a'        | $a          | variables are not expanded inside ''
 3 | "'$a'"      | 'apple'     | '' has no special meaning inside ""
 4 | '"$a"'      | "$a"        | "" is treated literally inside ''
 5 | '\''        | **invalid** | can not escape a ' within ''; use "'" or $'\'' (ANSI-C quoting)
 6 | "red$arocks"| red         | $arocks does not expand $a; use ${a}rocks to preserve $a
 7 | "redapple$" | redapple$   | $ followed by no variable name evaluates to $
 8 | '\"'        | \"          | \ has no special meaning inside ''
 9 | "\'"        | \'          | \' is interpreted inside "" but has no significance for '
10 | "\""        | "           | \" is interpreted inside ""
11 | "*"         | *           | glob does not work inside "" or ''
12 | "\t\n"      | \t\n        | \t and \n have no special meaning inside "" or ''; use ANSI-C quoting
13 | "`echo hi`" | hi          | `` and $() are evaluated inside ""
14 | '`echo hi`' | `echo hi`   | `` and $() are not evaluated inside ''
15 | '${arr[0]}' | ${arr[0]}   | array access not possible inside ''
16 | "${arr[0]}" | apple       | array access works inside ""
17 | $'$a\''     | $a'         | single quotes can be escaped inside ANSI-C quoting
18 | "$'\t'"     | $'\t'       | ANSI-C quoting is not interpreted inside ""
19 | '!cmd'      | !cmd        | history expansion character '!' is ignored inside ''
20 | "!cmd"      | cmd args    | expands to the most recent command matching "cmd"
21 | $'!cmd'     | !cmd        | history expansion character '!' is ignored inside ANSI-C quotes
---+-------------+-------------+--------------------------------------------------------------------
```

------

See also:

- [ANSI-C quoting with `$''` - GNU Bash Manual](https://www.gnu.org/software/bash/manual/html_node/ANSI_002dC-Quoting.html)
- [Locale translation with `$""` - GNU Bash Manual](https://www.gnu.org/software/bash/manual/html_node/Locale-Translation.html#Locale-Translation)
- [A three-point formula for quotes](https://stackoverflow.com/a/42104627/6862601)



