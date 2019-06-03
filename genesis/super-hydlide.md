# Super Hydlide

## RAM Locations

| Address | Size | Description                                             |
| ------- | ---- | ------------------------------------------------------- |
| $E000   |    8 | Name, ASCII space-padded (see "String Encoding")        |
| $E008   |    1 | Level                                                   |
| $E00A   |    2 | Life, maximum                                           |
| $E00C   |    2 | Life, current                                           |
| $E010   |    1 | Strength                                                |
| $E011   |    1 | ?                                                       |
| $E012   |    2 | Magic points, maximum                                   |
| $E014   |    2 | Magic points, current                                   |
| $E01A   |    1 | Attack point, current                                   |
| $E01B   |    1 | Attack point, base value                                |
| $E01C   |    1 | Armour class, current                                   |
| $E01D   |    1 | Armour class, base value                                |
| $E01E   |    1 | Agility, 1st value (??)                                 |
| $E01F   |    1 | Agility, 2nd value (??)                                 |
| $E020   |    1 | ?                                                       |
| $E021   |    1 | Dexterity                                               |
| $E022   |    1 | ?                                                       |
| $E023   |    1 | Intelligence                                            |
| $E024   |    1 | ?                                                       |
| $E025   |    1 | Luck                                                    |
| $E026   |    1 | ?                                                       |
| $E027   |    1 | Charm                                                   |
| $E028   |    1 | ?                                                       |
| $E029   |    1 | Mind Factor                                             |
| $E02A   |    1 | ?                                                       |
| $E02C   |    2 | Experience                                              |
| $E048   |    1 | Inventory item #1 type (see "Item Types")               |
| $E049   |    1 | Inventory item #1 attributes (see "Item Attributes")    |
| $E04A   |    1 | Inventory item #2 type (see "Item Types")               |
| $E04B   |    1 | Inventory item #2 attributes (see "Item Attributes")    |
| $E04C   |    1 | Inventory item #3 type (see "Item Types")               |
| $E04D   |    1 | Inventory item #3 attributes (see "Item Attributes")    |
|  ....   |  ... | ...                                                     |
| $E06E   |    1 | Inventory item #20 type                                 |
| $E06F   |    1 | Inventory item #20 quantity                             |
| $E078   |    1 | Money, $10 coins quantity                               |
| $E079   |    1 | Money, $100 coins quantity                              |
| $E07A   |    1 | Money, $1000 coins quantity                             |

# String Encoding

| Range        | Description                    |
| ------------ | ------------------------------ |
| 0x20         | Space (ASCII)                  |
| 0x21 to 0x3a | Uppercase A-Z                  |
| 0x3b         | Hyphen                         |
| 0x3c         | Exclamation mark               |
| 0x41         | Japanese maru character        |
| 0x42         | Japanese open-quote character  |
| 0x43         | Japanese close-quote character |
| 0x61 to 0x7a | Lowercase a-z (ASCII)          |
| 0x7b         | Period                         |
| 0x7e         | Comma                          |

# Item Types

| Value | Description            | Equippable? |
| ----- | ---------------------- | ----------- |
| $00   | Knife                  | Yes         |
| $01   | Dagger                 | Yes         |
| $02   | Broad sword            | Yes         |
| $03   | Holy sword             | Yes         |
| $04   | Silver sword           | Yes         |
| $05   | Flaming sword          | Yes         |
| $06   | Sling                  | Yes         |
| $07   | Light saber            | Yes         |
| $08   | Arrow                  | Yes         |
| $09   | Bow                    | Yes         |
| $0A   | Long bow               | Yes         |
| $0B   | Club                   | Yes         |
| $0C   | Ax                     | Yes         |
| $0D   | Battle-ax              | Yes         |
| $0E   | Leather mail           | Yes         |
| $0F   | Chain mail             | Yes         |
| $10   | Bronze mail            | Yes         |
| $11   | Silver mail            | Yes         |
| $12   | Magical mail           | Yes         |
| $13   | Fairy-made mail        | Yes         |
| $14   | Shield                 | Yes         |
| $1C   | Fairy-made helmet      | Yes         |
| $20   | Expensive medicine     | No          |
| $22   | Cure-all medicine      | No          |
| $25   | Money changer          | No          |
| $26   | Cross of God           | Yes         |
| $27   | Stone of warrior       | Yes         |
| $2A   | Talisman               | Yes         |
| $2B   | Ration of food         | No          |
| $3C   | ID card                | No          |
| $3D   | Cloud Stone            | Yes         |
| $42   | Space suit             | Yes         |
| $43   | Time-door talisman     | No          |
| $44   | Holy water             | No          |
| $46   | Bank book              | No          |
| $FF   | Empty/no item          | N/A         |

# Item Attributes

This 1 byte value is multi-purpose depending upon what "kind" of item is chosen:

* For usable items, the value is the raw quantity (0-255).

* For equippable or wearable items (ex. armour, weapons, etc.):
  * Bit 7: 1=equipped, 0=not equipped
  * Bits 6-4: unused, set to 0
  * Bits 3-0: quantity (0-15)

If a non-equippable item is marked as equipped (bit 7 set), the Use Tools menuing
system treats it as an equippable item and thus cannot be "used" (ex. medicine
can be equipped but never quaffed/consumed).  I have no idea what the underlying
game engine does with such mangled items; best not find out.

