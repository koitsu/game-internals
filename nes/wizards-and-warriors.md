# Wizards &amp; Warriors

Note: the below details **do not** apply to 伝説の騎士 エルロンド (Densetsu no
Kishi: Elrond).  RAM locations and values are substantially different in that
release of the game.

## RAM Locations

| Address | Size | Description                                    |
| ------- | ---- | ---------------------------------------------- |
| $0073   | 1    | Gems, hundredths (range 0-9)                   |
| $0074   | 1    | Gems (max 99)                                  |
| $0078   | 1    | Special Item (see "Special Item Types")        |
| $0079   | 1    | Throwable range and options (see "Throwables") |
| $062B   | 1    | Have Dagger of Throwing (0=no, 1=yes)          |
| $062C   | 1    | Have Shield of Protection (0=no, 1=yes)        |
| $062D   | 1    | Have Feather of Feather Fall (0=no, 1=yes)     |
| $062E   | 1    | Have Potion of Levitation (0=no, 1=yes)        |
| $062F   | 1    | Have Battle Axe of Agor (0=no, 1=yes)          |
| $0630   | 1    | Have Blue Key (0=no, 1=yes)                    |
| $0631   | 1    | Have Pink Key (0=no, 1=yes)                    |
| $0632   | 1    | Have Red Key (0=no, 1=yes)                     |

# Special Item Types

| Value | Description                                         |
| ----- | --------------------------------------------------- |
| $00   | None                                                |
| $01   | Crash on use                                        |
| $02   | Crash on use, displays Feather of Feather Fall icon |
| $03   | Bugged on use, displays Potion of Levitation icon   |
| $04   | None                                                |
| $05   | Wand of Wonder                                      |
| $06   | Cloak of Darkness                                   |
| $07   | Boots of Force                                      |
| $08   | None                                                |
| $09   | None                                                |
| $0A   | Horn                                                |
| $0B   | Map (unobtainable item)                             |
| $0C   | Staff of Power                                      |
| $0D   | Crash on use, displays a lightning bolt(?)          |
| ?     | Boots of Lava Walk                                  |
| Other | Bugged game or crashes game on use                  |

# Throwables

The Dagger of Throwing and the Battle Axe of Agor can be thrown at variable
distances (bits 1-0, thus values 0-3), depending on how many Small Knives or
Small Axes you've collected.  Higher value = further distance.

Bit 2, once set, allows for multiple Axes to be thrown at maximum distance
unconditionally.  (This multiple projectile capability does not apply to
Daggers).  Here's a chart to make it clearer:

| Value (binary)  | Description                                |
| --------------- | ------------------------------------------ |
| $00 (%000)      | Throw 1 dagger/axe, distance = ~100 pixels |
| $01 (%001)      | Throw 1 dagger/axe; distance = ~110 pixels |
| $02 (%010)      | Throw 1 dagger/axe; distance = ~120 pixels |
| $03 (%011)      | Throw 1 dagger/axe; distance = ~130 pixels |
| $04 (%100)      | Throw 2 axes; distance = ~130 pixels       |
| $05 (%101)      | Throw 2 axes; distance = ~130 pixels       |
| $06 (%110)      | Throw 2 axes; distance = ~130 pixels       |
| $07 (%111)      | Throw 2 axes; distance = ~130 pixels       |

Refer to the code at $E624-E64F (fire projectile) and $E020-E030 (collecting a
Small Knife or Small Axe).
