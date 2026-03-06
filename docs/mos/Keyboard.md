# Keyboard Input

This section will explain methods of reading the keyboard

## Method 1

This is a link [found here](../MOS.md#the-stack).

## Method 2

A chart will give lookups like this:

| Note | Size | Contents |
|--------|------|----------|
| Bit   | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
| 0x43   | 1 byte | F |
| 0x44   | 1 byte | J |


| Note | Size | Contents |
|     Bit|   7    |   6    |     5     |     4     |    3     |    2     |     1     |     0     |
|Index   |        |        |           |           |          |          |           |           |        
|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|
| 00     | CTRL R | SHIFT R| ALT L     | CTRL L    | SHIFT L  |          |           | ALT R     |
| 01     |        |        |           |           |          |          |           |           |
| 02     | -      | F7     | 8         | F4        | 5        | 4        | 3         | q         |
| 03     | Scroll | F10    | F12       | F11       | 7 (pad)  | 6 (pad)  |           | ⇐         |
| 04     | 0      | 9      | I         | 7         | T        | E        | W         | PRT SCR   |
| 05     | BK SPC | ` ~    |           | 9 (pad)   | 8 (pad)  |          |           | ⇓         |
| 06     | P      | O      | U         | 6         | R        | D        | 2         | 1         |
| 07     | PageUp | Home   | Insert    | Enter(pad)| - (pad)  | + (pad)  | ⇧         | [         |
| 08     | ‘(@)   | K      | J         | Y         | F        | X        | A         | CAPS LK   |
| 09     | PageDn | NUM LK | ./del(pad)| / (pad)   |          |          |           | ENTER     |
| 0A     | ;      | L      | N         | H         | G        | C        | S         |           |
| 0B     |        | - (+)  |           | *(pad)    |          |          |           | DELETE    |
| 0C     | . >    | , <    | M         | B         | V        | SPACE    | Z         | TAB       |
| 0D     |        |        | 3(pad)    | 1(pad)    | 0(pad)   | End      | / ?       |           |
| 0E     | F9     | F8     | F6        | F5        | F3       | F2       | F1        | ESC       |
| 0F     | WIN R  | WIN L  | 2(pad)    | 5(pad)    | 4(pad)   |          |           | ⇨         |
|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|

You can now read the keyboard
