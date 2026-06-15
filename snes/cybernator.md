# Cybernator

## PRG ROM Locations

| Address | Bytes       | Instruction | Description                                   |
| ------- | ----------- | ----------- | --------------------------------------------- |
| $80950D | A9 00 00    | lda #$0000  | Load starting stage number (range 0-17)       |
| $809513 | A9 01 00    | lda #$0001  | Load starting level of Weapons: Vulcan, Punch |
| $80956E | A9 03 00    | lda #$0003  | Load starting credit count                    |
| $80E024 | CE FA 01    | dec $01fa   | Decrement credit counter                      |
| $829372 | CE 58 19    | dec $1958   | Decrement overheat counter                    |

## WRAM Locations

| Address | Size | Description                                                     |
| ------- | ---- | --------------------------------------------------------------- |
| $0066   | 2    | Weapon: Vulcan level                                            |
| $0068   | 2    | Weapon: Laser level                                             |
| $006A   | 2    | Weapon: Missile level                                           |
| $006C   | 2    | Weapon: Punch level                                             |
| $006E   | 2    | Weapon: Napalm level                                            |
| $00E0   | 4    | Player name (ASCII) (e.g. "Jake")                               |
| $01FA   | 2    | Credits remaining                                               |
| $14CA   | 2    | Health (range 0-256) (0=dead) (see "Health and Damage Details") |
| $1958   | 2    | Overheat counter                                                |

## Health and Damage Details

For "unlimited health" (i.e. no damage taken), use a pair of PAR codes to set
health to 256 ($100):

```
0014CA00
0014CB01
```

However, be aware that stages which contain collision death capability (i.e.
asteroids which crush you against the left side of the screen) will be somewhat
bugged if dying via collision (the game will not end).

Some PAR codes found online, like `7E14CA20`, do not work correctly given how
health/ damage calculation is done.  See game code between $8293A1 and
$829438 for details.

Game Genie codes `82C9-470C` and `82C7-4FDC` attempt to disable damage being
taken by replacing `sta $000a,x` (9D 0A 00) with `lda $000a,x` (BD 0A 00).
However this is incomplete; there are **at least** 18 different pieces of code
scattered throughout the game that deal damage.  Below is an incomplete list of
PRG ROM addresses where writes to $14CA happen, when preceded by `sec/sbc`
(i.e. subtraction, or damage being dealt).

```
$82A5B4  9D 0A 00     sta $000a,x
$82A834  9D 0A 00     sta $000a,x
$82A85D  9D 0A 00     sta $000a,x
$82A875  9D 0A 00     sta $000a,x
$82A88D  9D 0A 00     sta $000a,x
$82A8AC  9D 0A 00     sta $000a,x
$82AC16  8D CA 14     sta $14ca
$82AEC3  8D CA 14     sta $14ca
$82AFA0  9D 0A 00     sta $000a,x
$82B1CC  8D CA 14     sta $14ca
$82B5EE  9D 0A 00     sta $000a,x
$82BF13  9D 0A 00     sta $000a,x
$82C099  9D 0A 00     sta $000a,x
$82C81B  9D 0A 00     sta $000a,x
$82C8A3  9D 0A 00     sta $000a,x
$82D245  8D CA 14     sta $14ca
$82D4B7  9D 0A 00     sta $000a,x
$82F5B2  8D CA 14     sta $14ca
```

## Code Complexities

* Programmers at NCS Soft were clearly familiar with 65816, as use of opcodes such
as `pei`, `pea,` `mvn`, `mvp`, and `cop` are found, as well as use of stack-indexed
addressing.

* Makes use of WRAM access via both direct page (D) and absolute addresses (via data
bank (PBR)) within the same code block.  For example, the health calculation subroutine
accesses WRAM $14CA via `$0a` (since D=$14C0), WRAM $14F4 via `$34`, and WRAM $01E8
directly (absolute addressing).

* Switches PBR (data bank) via `plb` everywhere, rather than full 24-bit addressing,
thus saving CPU cycles.

* Makes use of `cop` opcode and has actual code at the COP vector (points to $8846).

