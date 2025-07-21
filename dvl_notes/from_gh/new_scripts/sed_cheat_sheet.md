# sed Cheat Sheet

## Basic Usage
```
sed [options] 'command' file(s)
```

## Common Options
- `-n` - Suppress automatic printing
- `-e` - Add a script to be executed
- `-f` - Add the contents of a script file
- `-i` - Edit files in-place (use `-i.bak` to create backup)
- `-r` or `-E` - Use extended regular expressions

## Addresses (Line Selection)
- `n` - Line number n
- `$` - Last line
- `/regex/` - Lines matching regex
- `n,m` - Range from line n to m
- `n,+m` - Line n and the next m lines
- `n~m` - Every m-th line starting at line n

## Basic Commands
- `p` - Print current pattern space
- `d` - Delete pattern space
- `q` - Quit after current line
- `=` - Print line number
- `a\text` - Append text after line
- `i\text` - Insert text before line
- `c\text` - Change line to text
- `r file` - Append contents of file
- `w file` - Write pattern space to file

## Substitution (s command)
```
s/regex/replacement/flags
```
### Flags
- `g` - Global replacement
- `p` - Print if substitution made
- `i` or `I` - Case-insensitive match
- `w file` - Write to file if substitution made

### Examples
- `sed 's/foo/bar/'` - Replace first foo with bar on each line
- `sed 's/foo/bar/g'` - Replace all foo with bar
- `sed 's/foo/bar/2'` - Replace only second foo with bar
- `sed 's/foo/bar/gi'` - Replace all foo (case-insensitive) with bar

## Transform (y command)
- `y/abc/xyz/` - Translate a to x, b to y, c to z

## Multiple Commands
- Separate with `;` or use `-e` for each command
- `{ }` - Group commands for same address

## Examples
- `sed -n '10,20p' file` - Print lines 10-20
- `sed '/foo/d' file` - Delete lines containing "foo"
- `sed 's/foo/bar/g' file` - Replace all "foo" with "bar"
- `sed -i.bak 's/foo/bar/g' file` - In-place edit with backup
- `sed '/baz/s/foo/bar/g'` - Only substitute on lines containing "baz"
- `sed -e '1d' -e '$d' file` - Delete first and last line
- `sed '1~2d' file` - Delete every other line starting with line 1
- `sed '/^#/d' file` - Delete comment lines (starting with #)
- `sed 's/^[ \t]*//' file` - Remove leading whitespace
- `sed ':a;N;$!ba;s/\n/ /g' file` - Replace newlines with spaces

## Advanced
- `:label` - Define label
- `b label` - Branch to label
- `t label` - Branch if substitution made
- `n` - Read next line into pattern space
- `N` - Append next line to pattern space
- `D` - Delete first line of pattern space
- `P` - Print first line of pattern space
- `h` - Copy pattern space to hold space
- `H` - Append pattern space to hold space
- `g` - Copy hold space to pattern space
- `G` - Append hold space to pattern space
- `x` - Exchange hold and pattern spaces
