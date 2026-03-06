# Keyboard Input

There are several different methods of reading the keyboard from within your code, each with advantages and disadvantages, so the method selected will depend on your specific need at that point in your code. You don't have to stick with one method, you can choose the best for a particular task.

## Method 1 - mos_getkey

This call will wait until a key has been pressed, and return the ascii key code of the key which was pressed.

This method should be used with caution. Calling this method will wait indefinitely until a key is pressed, with no other means of escape, apart from a system reset.

In assembler, an example might be:

```
ld a, $00         ; put $00 into A
rst.lil $08       ; make a MOS call with command $00 (mos_getkey).
                  ; system will wait until a key is pressed, then...
                  ; A now contains the ascii code of the pressed key
```

The call has not been implemented in AgonDev C, as the same result could be achieved with the next method.


## Method 2 - sysvar_keyascii

The MOS API command `mos_sysvars` returns a pointer to the base of the MOS SysVars (system state variables/information) area in IXU as a 24-bit pointer. 

The byte at offset $05 after IXU provides an ascii code of the current key being pressed (or most recent if several are pressed), or 0 if no key is pressed.

This is useful to check for a single key press. E.g., in a game menu where there may be multiple options, but only a single decision is needed to move on.

In assembler, an example might be:

```
ld a, $08         ; put $08 into A
rst.lil $08       ; make a MOS call with command $08 (mos_sysvars).
                  ; IXU is now loaded with the base address
ld a, (ix + $05)  ; A is loaded with the byte at offset +$05 from the base address
                  ; A now contains the ascii code of the pressed key, or 0 if no key
```

In AgonDev C, the following example can be used:

```
#include "agon/vdp.h"

...

uint_8 theKey = vdp_getKeyCode();      // put ascii code, or 0, into  theKey

```


It is also possible to check the status of the modifier keys (SHIFT, CTRL, etc).

The byte at offset $06 after IXU provides a bit code of the modifier keys which are pressed.

```
ld a, $08         ; put $08 into A
rst.lil $08       ; make a MOS call with command $08 (mos_sysvars).
                  ; IXU is now loaded with the base address
ld a, (ix + $06)  ; A is loaded with the byte at offset +$06 from the base address
                  ; A now contains a bit patter of any modifier keys pressed
```

The following bits represent the given modifier keys:


| Bit |  Hex |Modifier |
| :---: | :---: | :---: |
| 0     | $01 | CTRL |
| 1     | $02 | SHIFT |
| 2     | $04 | ALT L |
| 3     | $08 | ALT R |
| 4     | $10 | CAPS |
| 5     | $20 | |
| 6     | $40 | |
| 7     | $80 | WINDOWS |


## Method  3 - mos_getkbmap

The MOS API command `mos_getkbmap` returns a pointer to the base address of the MOS _virtual keybaord map_ in IXU as a 24-bit pointer. 

The keyboard map is an array of 16 bytes, where each bit within those bytes contains the current status of each key on the keyboard, bit = 1 for pressed, bit = o for not pressed.

To find out if any key (including modifer keys) is pressed, read the correct byte with the offset after IXU and then check the specific bit for its status.

This method is useful to check for a multiple key presses. E.g., In a game menu where multiple directions are possible (up and right), or movement plus a fire button need to be detected at the same time. 

This can also be used to check for less common combinations that would not return a standard ascii character, such as pressing the LEFT ALT and SPACE at the same time to perform a special function.

In assembler, an example might be:

```
ld a, $1E             ; put $1E into A
rst.lil $08           ; make a MOS call with command $1E (mos_getkbmap).
                      ; IXU is now loaded with the base address of the keyboard map
ld a, (ix + $0C)      ; A is loaded with the byte at offset +$0C from the base address
                      ; A now contains the status of 8 differnt keys
bit 2, A              ; The Z flag register now determines whether the SPACE key (bit 2) is pressed
jp nz, SPACE_PRESSED  ; do something as a result of key status
```


In AgonDev C, the following example can be used:

```
#include "agon/vdp.h"
#define GETBIT(var, bit)	(( var & (1 << (bit) ) ) ? 1 : 0 )

...

uint8_t keyRead = vdp_getKeyMap($0C);   // read the key map from offset +$0C
if (GETBIT(keyRead, 2)) {               // SPACE key
  // do some code here
}
```

    
The following chart lists which key is defined for each _bit_ within each _byte_ offset from $00 to $0F:


| IX+\Bit |   7    |   6    |     5     |     4     |    3     |    2     |     1     |     0     |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| $00     | CTRL R | SHIFT R| ALT L     | CTRL L    | SHIFT L  |          |           | ALT R     |
| $01     |        |        |           |           |          |          |           |           |
| $02     | -      | F7     | 8         | F4        | 5        | 4        | 3         | q         |
| $03     | Scroll | F10    | F12       | F11       | 7 (pad)  | 6 (pad)  |           | ⇐         |
| $04     | 0      | 9      | I         | 7         | T        | E        | W         | PRT SCR   |
| $05     | BK SPC | ` ~    |           | 9 (pad)   | 8 (pad)  |          |           | ⇓         |
| $06     | P      | O      | U         | 6         | R        | D        | 2         | 1         |
| $07     | PageUp | Home   | Insert    | Enter(pad)| - (pad)  | + (pad)  | ⇧         | [         |
| $08     | ‘(@)   | K      | J         | Y         | F        | X        | A         | CAPS LK   |
| $09     | PageDn | NUM LK | ./del(pad)| / (pad)   |          |          |           | ENTER     |
| $0A     | ;      | L      | N         | H         | G        | C        | S         |           |
| $0B     |        | - (+)  |           | *(pad)    |          |          |           | DELETE    |
| $0C     | . >    | , <    | M         | B         | V        | SPACE    | Z         | TAB       |
| $0D     |        |        | 3(pad)    | 1(pad)    | 0(pad)   | End      | / ?       |           |
| $0E     | F9     | F8     | F6        | F5        | F3       | F2       | F1        | ESC       |
| $0F     | WIN R  | WIN L  | 2(pad)    | 5(pad)    | 4(pad)   |          |           | ⇨         |

Keys located on an extended keyboard number pad area are indicated with (pad).

NOTE: There are a few gaps, so there may be more keys as not every keyboard has been tested.

