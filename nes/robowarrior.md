# Robowarrior

## RAM Locations

| Address | Size | Description                                                         |
| ------- | ---- | ------------------------------------------------------------------- |
| $000E   | 1    | Controller 1 input (see "Controller Input")                         |
| $000F   | 1    | Controller 2 input (see "Controller Input")                         |
| $0011   | 1    | Bitwise NOT of $000E (probably used as a mask in code somewhere)    |
| $004C   | 1    | Current bomb count                                                  |
| $0078   | 1    | Player facing direction ($03=Up, $02=Down, $01=Left, $00=Right)     |
| $01B9   | 1    | Background music loop check ($01=Looped 1+ times, $00=Never looped) |
| $01C6   | 1    | Energy metre, 8-tick ($FF=Full, $7F=1 missing, $3F=2 missing, etc.) |
| $01C7   | 1    | Life pods ("tanks") ($0F=4, $07=3, $03=2, $01=1)                    |
| $01ED   | 1    | Debug mode (unlimited items) ($01=Enabled, $00=Disabled)            |
| $0528   | 1    | Last enemy killed (see "Enemies")      

# Controller Input

| Value | Description |
| ----- | ----------- |
| $80   | A button    |
| $40   | B button    |
| $20   | Select      |
| $10   | Start       |
| $08   | Up          |
| $04   | Down        |
| $02   | Left        |
| $01   | Right       |

# Enemies

| Value | Description                        |
| ----- | ---------------------------------- |
| $08   | Amoeba (not sure what type)        |
| $05   | Lurcher                            |
| $04   | Triangle-like enemy (name unknown) |
| $03   | Mecha-bird                         |
| $02   | Grunt                              |
| $01   | Skraug                             |

# Unlimited items mode

## History

As a kid, I learned of this trick from
[Nintendo Power (Issue #9, page 78)](https://archive.org/details/Nintendo_Power_Issue001-Issue127/Nintendo%20Power%20Issue%20009%20November-December%201989).
Years later I was surprised to see that GameFAQs and other websites on
Robowarrior had no information it.

There are some Japanese web pages that cover this trick, so it's safe to
assume outside of Nintendo Power, this wasn't well-known in the
English-speaking world:

* https://urawaza.in/fc/bomberking.htm
* http://marie.saiin.net/~capsule/game/waza_fc_ha.html
* http://www5f.biglobe.ne.jp/~famicom/Urawaza/FC/BomberKing.htm
* http://huurinsou.blog61.fc2.com/blog-entry-807.html (defunct)
* http://hirobee.oops.jp/famikontalk/mugenzou.htm (attempts to download files on visit)

In late 2016 I did
[a write-up of this trick](https://forums.nesdev.org/viewtopic.php?t=14846)
on the nesdev forum, but decided to import it here for safe keeping.

I've also amended it to include the relevancy of the background music
needing to loop at least once, which was reverse-engineered by ruadath on
the nesdev forum.

## The trick 

The following conditions must be met:

- Background music must have looped at least once
- Must have Megaton bomb from first idol room
- Must have exactly 62 bombs
- Must have full energy
- Must have exactly 4 life pods
- Must shoot/kill a Lurcher
- ...with the normal weapon only
- ...while facing downward (final shot that kills the Lurcher must be done while facing down)
- Press Select to go into the Inventory screen
- Move the cursor to Energy Tank item (far left, 2nd from top), but DO NOT use it yet!
- On Controller 2 press and hold Up and Left
- On Controller 1 press A twice (attempt to use the Energy Tank)

## 6502 code

The code which requires all these conditions is in the last (8th) PRG bank of the
game, which is hard-wired to $C000-FFFF in ROM space.  The subroutine is $C6B0:

```
L_C6B0: lda $0528           ; Ensure last enemy killed was a Lurcher
        cmp #$05            ; ...
        bne L_C6E1          ; ...
        lda $01B9           ; Ensure background music has looped at least once
        beq L_C6E1          ; ...
        lda $0F             ; Ensure Controller 2 is holding Up + Left
        cmp #$0A            ; ...
        bne L_C6E1          ; ...
        lda $01C6           ; Ensure energy tank is full
        cmp #$FF            ; ...
        bne L_C6E1          ; ...
        lda $01C7           ; Ensure life pods is at 4
        cmp #$0F            ; ...
        bne L_C6E1          ; ...
        lda $78             ; Ensure player is facing downward
        cmp #$02            ; ...
        bne L_C6E1          ; ...
        lda $4C             ; Ensure current bomb count is 62 ($3E)
        cmp #$3E            ; ...
        bne L_C6E1          ; ...
        lda #$01            ; If all the above conditions are met,
        sta $01ED           ; enable debug mode
L_C61E: rts
```

# Miscellaneous

* Values greater than $01 for debug mode (i.e. $02 to $FF) are treated as if
debug mode is enabled.  Other subroutines which use $01ED only bother to check
if the value is either zero (BEQ) or non-zero (BNE).

