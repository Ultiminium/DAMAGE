# DAMAGE

**DAMAGE** is a coding language written around how I think, which is very dumb. Because I think stupidly.  Which means anyone can learn **DAMAGE**.

Programs are written in `.damg` files and run with the `damage` command.

```
| hello world
@print+
     +Hello, world!-
@-
```

---

## Download

**[damage-website.vercel.app/download.html](https://damage-website.vercel.app/download.html)**

Or grab the latest release directly from [GitHub Releases](https://github.com/Ultiminium/DAMAGE/releases).

---

## Quick install — Linux

```bash
# Download and extract the binary, then:
chmod +x damage
sudo mv damage /usr/local/bin/damage
damage --version
```

---

## The basics

Every block opens with `@functionname+` and closes with `@-`. Content is indented 5 spaces. Anything after `|` is a comment.

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
     name+Alice-
     score+100-
     alive=1
@-
```

Booleans use `=`. Change with `@dyn`:

```
@dyn,var+
@dyn:(score) = (score)+10
@-
```

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
     +2^10 = %2^10%? The answer is %-
@-
```

Random OR — picks one:

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

Operators: `+ - * / ^`. Exponents are right-to-left: `2^3^2 = 512`.

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

Operators: `=` `<` `>` `~` (not equal). Chain AND with commas: `if, (a)=1,(b)=2`

---

## Loops

```
@loop,print+
     +Hello!-
@loop:3
@-
```

Variable count: `@loop:(times)`. Zero skips entirely.

---

## User input

```
@user,print+
     +Enter a number:-
@user:expect"n"
@var:create+num-
@-
```

`"n"` = numbers only. `"l"` = letters only. Re-prompts until valid.

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

---

## Fetch

Pipe data from anywhere to anywhere:

```
@fetch+
fetch.+@var/score-$+@var/backup-
fetch.+gui:1-$+@var/input-
@-
```

---

## Block chaining

```
@dyn,var+
@dyn:(n) = (n)+1
@-&
@print,var+
     +(n)-
@-#
```

`@-&` = close and continue. `@-#` = end of chain.

---

## Text operations

```
| get length
@text+
@text:length+@var/name-
@var:create+len-
@-

| capitalize
@text+
text:capital"@var/name/s":1
@-

| extract numbers from mixed string
@text+
@text:length+@var/input-
text:check"n"
@var:create+nums-
@-

| extract letters from mixed string
@text+
@text:length+@var/input-
text:check"l"
@var:create+letters-
@-
```

`text:check"n"` extracts numeric characters. `text:check"l"` extracts letter characters. Returns `0` if none found.

---

## Encryption

DAMAGE uses **ChaCha20-Poly1305 + custom XOR/shuffle** — industry-grade layered encryption. The password is never stored.

```
| two-way encryption
@enc,var+
     +(secret)-
@enc:password"mykey"
@var:create+encrypted-
@-

@dec,var+
     @dec:@var/encrypted/mykey
@var:create+decrypted-
@-

| one-way (no decryption)
@enc,var+
     +(password)-
@var:create+hashed-
@-
```

Wrong password → `[DECRYPT ERROR: wrong password or corrupted data]`

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

```bash
damage install dice
damage list
damage uninstall dice
```

Packages auto-load on every run. No import needed.

Available packages: `math` `string` `validate` `dice` `cards` `format` `table` `color`

---

## CLI

```
damage <file.damg>       run a program
damage --help            full syntax guide
damage --version         show version
damage --check           syntax check without running
damage --debug           show tokens then run
damage --log             always save errors to log
damage --no-log          never save errors
damage --list-funcs      list saved user functions
damage --clear-logs      delete all logs
damage --clear-funcs     delete all user functions
damage install <name>    install a package
damage uninstall <name>  uninstall a package
damage list              list installed packages
```

---

## Built-in functions

| Function | What it does |
|----------|-------------|
| `print` | output text |
| `var` | declare variables |
| `math` | math operators |
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

## DAMAGE Studio

DAMAGE Studio is the official IDE for DAMAGE. Write, run, and manage `.damg` programs with syntax highlighting, integrated output, package management, and built-in documentation.

**[Download DAMAGE Studio](https://damage-website.vercel.app/download.html)**

---

## Links

- **Website** — [damage-website.vercel.app](https://damage-website.vercel.app)
- **Documentation** — [damage-website.vercel.app/docs.html](https://damage-website.vercel.app/docs.html)
- **Releases** — [github.com/Ultiminium/DAMAGE/releases](https://github.com/Ultiminium/DAMAGE/releases)
- **Package registry** — [github.com/Ultiminium/DAMAGE-Packages](https://github.com/Ultiminium/DAMAGE-Packages)
- **Website repo** — [github.com/Ultiminium/DAMAGE-Website](https://github.com/Ultiminium/DAMAGE-Website)

---

## License

MIT — free to use, modify, and distribute.

---

*DAMAGE v8.3 — Native Rust binary. Zero dependencies.*
