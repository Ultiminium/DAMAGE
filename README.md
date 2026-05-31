# DAMAGE

DAMAGE is a programming language designed around how you naturally think. No type errors. No confusing syntax. Just write what you mean.

Programs are written in `.damg` files and run with the `damage` command.

---

## Installing

### Standalone binary (recommended)
Download the binary from [Releases](https://github.com/Ultiminium/DAMAGE/releases), then:

```bash
chmod +x damage
sudo mv damage /usr/local/bin/damage
```

### Build from source
Requires [Rust](https://rustup.rs):

```bash
git clone https://github.com/Ultiminium/DAMAGE
cd DAMAGE/damage-rs
cargo build --release
sudo mv target/release/damage /usr/local/bin/damage
```

---

## Running a program

```bash
damage yourfile.damg
```

---

## The basics

Every block opens with `@functionname+` and closes with `@-`. Content inside is indented 5 spaces. Anything after `|` is a comment.

```
| this is a comment
@print+
     +Hello world-
@-
```

---

## Variables

```
@var+
     name+User-
     score+0-
     isAlive=1
@-
```

Booleans use `=` instead of `+value-`. `1` is on, `0` is off.

To change a variable mid-program:

```
@dyn,var+
@dyn:(score) = (score)+10
@-
```

---

## Referencing variables

The position of `var` in the function list determines how many parens you use:

```
@print,var+
     +(name) scored (score) points-
@-
```

```
@print,math,var+
     +((score)) (+) 1-
@-
```

`var` at position 2 → `((score))`. `math` at position 1 → `(+)`.

---

## Print

```
@print+
     +Hello world-
@-

@print,var+
     +Hello (name)!-
@-

@print,math+
     +2 (+) 2 = 4-
@-
```

Inline math with `%` — define between `%%`, result placed at the next `%`:

```
@print,math+
     +2^10 = %2^10%? The answer is %-
@-
```

Random OR — picks one randomly:

```
@+Good job!-?@+Well done!-?@+Amazing!-
```

---

## Math

```
@math+
     10 + 5
@-
```

Operators: `+ - * / ^`

Exponents are right-to-left: `2^3^2 = 512` (= 2^9)

Results above 10^10 display in scientific notation. Results too large display as `too large to display`.

---

## Conditions

```
if, (score) = 100
@print+
     +Perfect!-
@-
other if, (score) > 50
@print+
     +Not bad.-
@-
other,
@print+
     +Keep trying.-
@-
```

Operators: `=` `<` `>` `~` (not equal)

Chained AND:

```
if, (score) = 100,(lives) > 0
```

---

## Loops

```
@loop,print+
     +Hello!-
@loop:3
@-
```

Variable loop count:

```
@loop,print+
     +Hello!-
@loop:(times)
@-
```

`@loop:0` skips entirely.

---

## User input

```
@user,print+
     +What is your name?-
@var:create+name-
@-
```

With validation — re-prompts until valid:

```
@user,print+
     +Enter a number:-
@user:expect"n"
@var:create+num-
@-
```

`"n"` = numbers only, `"l"` = letters only.

---

## Random

```
@print,random+
     @()+1,2,3,4,5-
@var:create+pick-
@-

@print,random+
     @()+1-100-
@var:create+pick-
@-
```

---

## User functions

```
@addfunc+
@print+
     +Hello from my function!-
@-
@addfunc:createname"greet"
@---

@greet+
@-
```

Functions are saved as `.func` files. Variables are shared with the caller.

---

## Fetch

Pipe data from anywhere to anywhere using `$`:

```
@fetch+
fetch.+@var/score-$+@var/backup-
fetch.+gui:1-$+@var/input-
fetch.+@var/name-$+/path/out.txt-
fetch.+/path/in.txt-$+@var/data-
@-
```

---

## Block chaining

```
if, pressable+Calculate-:true
     @fetch+
     fetch.+gui:1-$+@var/expression-
@-&
@math,var+
     +(expression)-
@-&
@fetch+
     fetch.+@var/__mathresult__-$+gui:2-
@-#
```

`@-&` = close and continue. `@-#` = end of chain.

---

## Text operations

```
| get length
@text+
@text:length+@var/name-
@var:create+namelen-
@-

| capitalize letter at position
@text+
text:capital"@var/name/s":1
@-

| lowercase letter at position
@text+
text:lower"@var/name/S":1
@-

| error if exceeds limit
@text+
text:maxlength"15"
@-

| check if number or letters
@text+
@text:length+@var/input-
text:check"n"
@var:create+isnum-
@-
```

---

## Encryption

```
| one-way (no decryption)
@enc,var+
     +(password)-
@var:create+encrypted-
@-

| two-way
@enc,var+
     +(password)-
@enc:password"mykey"
@var:create+secured-
@-

| decrypt
@dec,var+
     @dec:@var/secured/mykey
@var:create+original-
@-
```

The password is never stored.

---

## GUI

```
@gui,var+
@gui:winname"My App"
@gui:winsize"800:600"
@gui:text+Hello-:pos+100,300-
@gui:input:writableelement:text+Type here...-:1
@gui:pressable"name:Click Me":pos+300,200-
if, pressable+Click Me-:true
     @+Button clicked!-
@-
```

Coordinates: origin bottom-left, X right, Y up.

---

## File output

```
@output,var+
     +(result)-
@output:createname"results.txt"
@-
```

---

## Import

```
@import+
     +utils.damg-
@-
```

---

## Clear

```
@clear+
@-
```

---

## Packages

```
damage install name
damage uninstall name
damage list
```

Packages auto-load on every run. No import needed.

---

## Errors

```
ERROR:
line.N, error "description"

Save to logs? [y/n]
```

Logs saved to `~/damage/logs/damage.log`.

---

## CLI

```
damage <file.damg>       run a program
damage --help            full syntax guide
damage --version         show version
damage --check           syntax check without running
damage --debug           show tokens then run
damage --log             always save errors
damage --no-log          never save errors
damage --list-funcs      list saved user functions
damage --clear-logs      delete all logs
damage --clear-funcs     delete all user functions
```

---

## Built-in functions

| Function | What it does |
|----------|-------------|
| `print` | output text |
| `var` | declare variables |
| `math` | math operators and evaluation |
| `loop` | repeat a block |
| `user` | get input |
| `random` | pick randomly |
| `output` | write to file |
| `gui` | open a window |
| `dyn` | reassign variables |
| `addfunc` | define a function |
| `text` | string operations |
| `fetch` | pipe data anywhere |
| `enc` / `dec` | encrypt and decrypt |
| `clear` | clear the terminal |
| `import` | import another file |

---

## Version

**v8.3**

## Links
**Website**
https://damage-website.vercel.app
**Website Repo**
https://github.com/Ultiminium/DAMAGE-Website
**Package Repo**
https://github.com/Ultiminium/DAMAGE-Packages
