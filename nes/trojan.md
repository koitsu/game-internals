# Trojan

## RAM Locations

| Address | Size | Description                                                      |
| ------- | ---- | ---------------------------------------------------------------  |
| $0200   | 256  | Sprite DMA region                                                |
| $0301   | 1    | Frame counter                                                    |
| $0306   | 1    | RNG buffer index/offset (see "Trojan's pseudo RNG")              |
| $0320   | 1    | Player 1 score, millionths (range 0-9)                           |
| $0321   | 1    | Player 1 score, hundred thousandths (range 0-9)                  |
| $0322   | 1    | Player 1 score, ten thousandths (range 0-9)                      |
| $0323   | 1    | Player 1 score, thousandths (range 0-9)                          |
| $0324   | 1    | Player 1 score, hundreths (range 0-9)                            |
| $0325   | 1    | Player 1 score, tenths (range 0-9)                               |
| $0326   | 1    | Player 2 score, millionths (range 0-9)                           |
| $0327   | 1    | Player 2 score, hundred thousandths (range 0-9)                  |
| $0328   | 1    | Player 2 score, ten thousandths (range 0-9)                      |
| $0329   | 1    | Player 2 score, thousandths (range 0-9)                          |
| $032A   | 1    | Player 2 score, hundreths (range 0-9)                            |
| $032B   | 1    | Player 2 score, tenths (range 0-9)                               |
| $032C   | 1    | High score, millionths (range 0-9)                               |
| $032D   | 1    | High score, hundred thousandths (range 0-9)                      |
| $032E   | 1    | High score, ten thousandths (range 0-9)                          |
| $032F   | 1    | High score, thousandths (range 0-9)                              |
| $0330   | 1    | High score, hundreths (range 0-9)                                |
| $0331   | 1    | High score, tenths (range 0-9)                                   |
| $0332   | 1    | Time, hundredths place (range 0-9)                               |
| $0333   | 1    | Time, tenths place (range 0-9)                                   |
| $0334   | 1    | Time, ones place (range 0-9)                                     |
| $0335   | 1    | Current player health / Player 1 health (VS Mode) (see "Health") |
| $0336   | 1    | Player 2 health (VS Mode only) (see "Health")                    |
| $03CB   | 1    | Current player jump boots counter (see "Jump Boots")             |
| $03CC   | 1    | Current player speed boost (0=no, 1=yes) (see "Speed")           |
| $03CD   | 1    | Current player damage modifier (see "Damage Modifier / P item")  |
| $03D0   | 1    | Current player lives remaining (max 9) (see "Lives")             |
| $03D1   | 1    | Phase number / boss type (see "Boss Types")                      |
| $03E0   | 1    | Player 1 lives remaining (max 9) (see "Lives")                   |
| $03F0   | 1    | Player 2 lives remaining (max 9) (see "Lives")                   |
| $062B   | 1    | Armadillon roll/loop count (see "Armadillon roll count")         |

## ROM Locations

| Address Range | Description                                               |
| ------------- | --------------------------------------------------------- |
| $B8B5-B8B6    | "P" power-up level 0 sprite palette values: `$00 $01`     |
| $B8B7-B8B8    | "P" power-up level 1 sprite palette values: `$00 $02`     |
| $FF37         | Random number generator (see "Trojan's pseudo RNG")       |

# Health

Player health normally ranges from 0 (dead) to 8 (maximum).  The status bar
only displays values up to 8, however the game code actually handles values
going all the way up to 255 ($FF) without issue.

RAM location $0335 is used for the current player health (in 1-player and
2-player modes), and also for Player 1 health in VS Mode.

# Lives

The lives-remaining counters are a bit strange, particularly in 2-Player mode.
Experiment with the values (especially how $03F0 fits into the picture) to get
a better idea.  I'm not able to easily explain it.

# Jump Boots

RAM location $03CB controls whether or not your player has jump boots or not,
but the value actually a decrementing counter.  When picking up jump boots, the
value is set to $0E.  The value will gradually decrement (once per tick on the
timer).  Upon reaching $02, the item in the status bar will begin to flash.
Once reaching $00, the power-up is removed.  Values greater than $0E do work.

# Speed

RAM location $03CC controls whether or not the player currently has the speed
item ("S").  Valid values are 0 (normal) and 1 (fast).  Values other than 1 are
accepted (and will be treated as fast), however the status bar will not depict
the speed boost via "S".

# Boss Types

RAM location $03D1 controls two aspects of the game:

1. The current "phase" or "sub-stage" number, starting at $00 for stage 1
2. The "boss type" associated with the current sub-stage

If changing/forcing this value whilst within a stage, you will begin
encountering enemies intended for that stage, often with messed up graphics.

If doing so immediately before reaching the final boss of the stage, you can do
things like fight Goblin way earlier than usual, or Achilles on stage 1.

Some values may cause your character to not be able to progress further, due to
those values relating to the "vertical" stages and underlying quirks/bugs.
There may be additional RAM locations that need tweaking to get those to work.

| Value | Description                              |
| ----- | ---------------------------------------- |
| $00   | Mamushi                                  |
| $01   | Iron Arm                                 |
| $02   | Armadillon                               |
| $03   | Goblin                                   |
| $04   | Muscler                                  |
| $05   | Trojan                                   |
| $06   | Iron Arm                                 |
| $07   | Goblin                                   |
| $08   | Muscler (buggy due to vertical stage)    |
| $09   | Iron Arm (buggy due to vertical stage)   |
| $0A   | Trojan                                   |
| $0B   | King Shriek                              |
| $0C   | Achilles                                 |
| Other | Crashes game                             |

# Damage Modifier / P item

RAM location $03CD contains a value which, under normal gameplay circumstances,
ranges from 0 to 2.  This value is added to your base damage of 1.  Example: a
value of 2 in this location means your attacks will do 3 total damage (1 base
damage + 2 modifier).

Picking up a "P" power-up item increments this number by 1.

This value also influences the palette used in the "P" power-up sprite,
allowing it to flash between red/white (if $03CD is $00) or blue/white (if
$03CD is $01 or larger).  The flashing animation is used in combination with
RAM location $0301 (frame counter) to alternate the sprite palette every 4
frames, alternating between a sprite palette value of $00 and $02.  These
palette values are stored in ROM at $B8B5-B8B8.  This is easier explained by
looking at the code; see $B86C-B8A7 for details.

Be aware that force-setting RAM location $03CD to a value of 0 when previously
at a higher value *will not* remove the power-up indicator in your status bar.
Instead, the last-shown-tile will remain.  This is not a bug, as the game is
not designed to handle the possibility of $03CD decrementing since this can't
happen during normal gameplay.

# Armadillon roll count

How many times Armadillon decides to roll back and forth is controlled by RAM
location $062B.  The value is decremented every time he hits a wall (side of
the screen), and will pop out/attack once the value reaches $00.  If the value
is already zero, it essentially means "do one roll".  Herein I'll refer to this
as the "loop count".

Location $062B is also used temporarily to store some data for animation or
sprites or something irrelevant to the counter RNG value.  I note this here for
anyone wanting to reverse engineer the code a bit more.

How the loop count is populated is surprisingly simple: it's based on a pseudo
RNG function that's used throughout the game.

The code at $A5AE-A5B3 (routine $A5A9-A5CD) is responsible for updating the
loop count (comments mine):

```
jsr $FF37               ; call the pseudo RNG, result in A
and #$03                ; limit its range to 0-3
sta $062B               ; store in Armadillon roll/loop counter
```

For details about $FF37, see the "Trojan's pseudo RNG" section elsewhere in
this document.

In short: sadly, there does not seem to be a way to circumvent the way this
works.  So to speedrunners looking for a way to guarantee Armadillon only rolls
*once*: there doesn't seem to be a consistent way, sorry.  (You would need to
bypass the use of the frame counter as an added value, in addition to somehow
ensuring the contents of $0310-031F were all zero.)

# Trojan's pseudo RNG

Trojan's random number generator is very basic (some might say stupid), but
it's "good enough" given the kind of game it is.

The subroutine is $FF37.  Here is its code (comments mine):

```
lda $0306               ; load A with $0306
and #$0F                ; limit value range to 0-15 (i.e. $0310-031F)
tax                     ; A --> X
inc $0306               ; increment $0306
lda $0310,X             ; load $0310+X
clc                     ; ...
adc $0301               ; add frame counter to A
sta $0310,X             ; write A to $0310+X
rts
```

RAM locations $0310-031F (16 bytes) are used as a circular buffer containing
random values that were previously populated (more on that in a moment).  The
use of a 16-byte buffer allows for multiple RNG calls in a single NMI to be
used without worrying about, for example, multiple enemies getting the same
random number (there is always that chance with this model, but it's less
likely to happen).

$0301 is the frame counter, which is a byte in RAM that is incremented every
frame (i.e. once per NMI/VBlank), thus has a range of 0-255.

$0306 is incremented every time `jsr $FF37` is run.  Its value is limited to a
range of 0-15 (matching the circular buffer size).  The value is initialised to
$00 at the start of boss fights.  Think of $0306 as an index/offset into the
$0310-031F region.

The existing value at location `$0310+{whatever $0306 is}` is read, the frame
counter added to it, and written back to the same location.  Rinse lather
repeat every time the subroutine is called.

On system reset, the contents of $0310-031F are initialised using a very basic
subroutine at $C061:

```
    ldy #$10            ; Y = 16
    ldx #$00            ; A = $00
L1: lda $C070,X         ; pre-generated buffer in ROM
    sta $0310,X         ; write raw values to $0310+X
    inx                 ; X++
    dey                 ; Y--
    bne L1              ; repeat 16 times
    rts
```

The values at ROM $C070-C07F are below.  There is nothing special about them,
nor their offset/placement (ex. value $0B is at "offset" $0B), other than the
fact that they are not sequentially incremental:

```
02 08 0D 09 0F 01 07 04 03 0A 06 0B 05 0E 00 0C
```

# Miscellaneous

* Score will wrap from 9,999,999,900 to 0

# Other reverse engineering notes

* Trojan uses fixed sprite numbers for bosses, i.e. no [OAM cycling](https://www.kickstarter.com/projects/1101008925/lizard/posts/1582636#h:flickering)
* Sprite size 8x8 is used exclusively, i.e. no use of 8x16
* [OAM DMA](https://wiki.nesdev.com/w/index.php/PPU_OAM#DMA) is used heavily, using page 2 (RAM $0200-02FF) as the source
* There are no boss screens that involve vertical movement, so we can use sprite X position for a RE starting point
* Use of save states is extremely helpful

