# DAMAGE

DAMAGE is a programming language with its own syntax, CLI, and GUI support. Programs are written in `.dmg` files and run with the `damage` command.

---

## Installing

### Standalone binary (no Python needed)
Download the binary from Releases, then:

```bash
chmod +x damage
sudo mv damage /usr/local/bin/damage
```

### From source
Requires Python 3.8+

```bash
git clone https://github.com/yourusername/damage
cd damage/damage_pkg
pip install -e .
```

---

## Running a program

```bash
damage yourfile.dmg
```

---

## The basics

Every block in DAMAGE opens with `@functionname+` and closes with `@-`. Content inside a block is indented 5 spaces. Anything after `|` on a line is a comment.

```
@print+
     +Hello world-
@-
```

---

## Variables

```
@var+
     name+Spencer-
     score+0-
     isAlive=1
@-
```

Booleans use `=` instead of `+value-`. `1` is on, `0` is off.

List variables are just a variable with commas in the value — useful for displaying a bunch of related things in one call:
```
@var+
     groceries+apples,bananas,milk,bread-
@-
```

To change a variable mid-program:
```
@dyn,var+
@dyn:(score) = (score)+10
@-
```

---

## Referencing variables

The position of `var` in the function list determines how many parens you wrap around the name.

```
@print,var+
     +(name) scored (score) points-
@-
```

`print` is position 0, `var` is position 1, so one layer of parens.

```
@print,math,var+
     +((score)) (+) 1-
@-
```

`var` is now position 2, so two layers. `math` is position 1, so `(+)` for the operator.

DAMAGE is forgiving about this — if it recognizes the content as a variable name it'll resolve it regardless of depth.

---

## Print

```
@print+
     +Hello world-
@-

@print,var+
     +Hello (name)-
@-

@print,math+
     +2 (+) 2 = 4-
@-
```

Inline math with `%` — define the expression between `%%`, result appears at the next standalone `%`:
```
@print,math+
     +2(+)2 = %2 (+) 2%? Correct, the answer is %-
@-
```
Output: `2+2 = ? Correct, the answer is 4`

---

## Math

```
@math+
     10 + 5
@-
```

Operators: `+ - * / ^`

Exponents are right-to-left like standard math: `2^3^2 = 512` (2^(3^2) = 2^9)

Numbers >= 10^10 automatically display in scientific notation. Numbers too large to compute display as `too large to display`.

---

## Conditions

```
if, (score) = 5
@print+
     +Perfect!-
@-
other if, (score) > 5
@print+
     +Too high-
@-
other,
@print+
     +Too low-
@-
```

First match wins — once a branch fires the rest are skipped.

Operators: `=` `<` `>` `~` (not equal)

Chain multiple conditions with AND using commas:
```
if, (score) = 5,(lives) > 0,(name) = Spencer
```

---

## Loops

```
@loop,print+
     +Hello!-
@loop:3
@-
```

`@loop:0` skips entirely. Conditional count:
```
@loop,print+
     +Hello!-
if, (score) = 5
@loop:3
if, (score) ~ 5
@loop:0
@-
```

---

## User input

```
@user,print+
     +What is your name?-
@var:create+name-
@-
```

Whatever the user types gets stored in `name`. If they type nothing, the value is `0`.

---

## Random

Pick from a list:
```
@print,random+
     @()+1,2,3,4,5-
@var:create+pick-
@-
```

Pick from a range:
```
@print,random+
     @()+1-100-
@var:create+pick-
@-
```

Random OR — picks one of these randomly wherever you put it:
```
@+Good job!-?@+Well done!-?@+Amazing!-
```

---

## Multi-statement

`&` lets a condition trigger multiple things in order:
```
if, (score) = 5
     @+You win!-&@+Score saved.-&@+Well done.-
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

Functions are saved as `.func` files in `damage/usrfunc/`. They share variables with the caller. You can't name a function the same as a built-in.

---

## File output

```
@output,var+
     +(result)-
@output:createname"results.txt"
@-
```

Saved to `damage/outputs/`.

---

## Fetch

Fetch pipes data from one place to another using `$`. Left side is the source, right side is the destination.

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

Chain multiple blocks together with `@-&`. End the chain with `@-#`.

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

---

## Text operations

```
@text+
@text:length+@var/name-
@var:create+namelen-
@-

@text+
text:capital"@var/name/s":1
@-

@text+
text:lower"@var/name/S":1
@-

@text+
text:maxlength"15"
@-
```

`@text:length` also accepts `/file/path` and `@user` as sources.

---

## GUI

```
@gui,var+
@gui:winname"My App"
@gui:winsize"800:600"
@gui:text+Hello -:pos+100,300-
@gui:input:writableelement:text+Type here...-:1
@gui:pressable"name:Click Me":pos+300,200-
if, pressable+Click Me-:true
     @+Button was clicked!-
@-
```

Coordinates start at the bottom-left. X goes right, Y goes up. Elements have IDs (`:1`, `:2`) for referencing them with fetch.

---

## Import

```
@import+
     +utils.dmg-
@-
```

Runs another `.dmg` file in the same context — variables and functions carry over.

---

## Clear

```
@clear+
@-
```

---

## Errors

When something goes wrong the program stops immediately:
```
ERROR:
line.N, error "description"

Save to logs? [y/n]
```

Logs go to `~/damage/logs/damage.log`.

---

## CLI flags

```
damage <file.dmg>           run a program
damage --help               full syntax guide
damage --version            show version
damage --check <file.dmg>   check syntax without running
damage --debug <file.dmg>   show tokens and AST, then run
damage --quiet <file.dmg>   suppress output except errors
damage --log                always save errors to logs
damage --no-log             never save errors to logs
damage --output <dir>       custom output directory
damage --list-funcs         list all saved user functions
damage --clear-logs         delete all logs
damage --clear-funcs        delete all user functions
```

---

## Built-in functions

|----------|-------------|
| `var` | declare variables |
| `loop` | repeat a block |
| `random` | pick randomly |
| `gui` | open a window |
| `addfunc` | define a function |
| `fetch` | pipe data anywhere |
| `import` | import another file |

---

## Version

**v6.0**
