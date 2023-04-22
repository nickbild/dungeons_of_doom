# Dungeons of Doom

Dungeons of Doom is a hack of the TRS-80 Color Computer classic Dungeons of Daggorath.  I added in some characters from Doom and enabled joystick-based controls, among other things.

![](https://raw.githubusercontent.com/nickbild/dungeons_of_doom/main/media/capture2.png)

## About the Game

Dungeons of Daggorath was a very popular first-person RPG in the early 1980s.  It was compatible with the Tandy TRS-80 Color Computer 1/2/3.

It was a very early three-dimensional game that allowed the player to explore a dungeon, and battle monsters via a text based interface.  That interface is used to move around, fight, pick up or use items, and much more.  However, the vast majority of the time, the commands entered revolve around moving and attacking.

For these common actions, the text-based interface can be a bit slow and clunky, so I modified the source code to enable the right joystick.  Moving the joystick moves the player around the dungeon, just as would be expected.  The button triggers the command `ATTACK RIGHT`.

Being a first-person game, I could not resist the temptation to insert a few Doom characters in place of some of the originals.  The CoCo may not be able to play Doom, but it kinda-sorta can do something like it.

I also gave the player unlimited power and health, which is great for exploring and testing, but that boost can be left out for those that want the challenge.

After assembly, the binary can be burned to a ROM chip mapped into the cartridge port, or should also work with any decent emulator.  I assembled to source code with `LWASM`.

Speaking of source code, the former CEO of DynaMicro, Inc. very graciously released the source code for public distribution, but only in its original form.  Since I have modified it, I cannot distribute my work.  However, one can still recreate Dungeons of Doom without too much trouble by:

1) Downloading the [original 6809 source code](https://github.com/MichaelSpencerJr/DungeonsOfDaggorath).
2) Making the modifications I have listed below.
3) Assembling the code.

## Media

Screencaps of modified enemies:

![](https://raw.githubusercontent.com/nickbild/dungeons_of_doom/main/media/capture1.png)
![](https://raw.githubusercontent.com/nickbild/dungeons_of_doom/main/media/capture2.png)

For comparison:

![](https://raw.githubusercontent.com/nickbild/dungeons_of_doom/main/media/capture1_comparison.png)
![](https://raw.githubusercontent.com/nickbild/dungeons_of_doom/main/media/capture2_comparison.png)


Playing on my CoCo 2:

![](https://raw.githubusercontent.com/nickbild/dungeons_of_doom/main/media/play1_sm.jpg)
![](https://raw.githubusercontent.com/nickbild/dungeons_of_doom/main/media/play2_sm.jpg)
![](https://raw.githubusercontent.com/nickbild/dungeons_of_doom/main/media/play3_sm.jpg)
![](https://raw.githubusercontent.com/nickbild/dungeons_of_doom/main/media/play4_sm.jpg)

## Modifications to the Original Source Code

- In `CD.ASM`:

After line 623, insert the following:

```
TEMP1   RMB     1                ; NAB
BAGP    RMB     2                ; NAB
```

- In `HUMAN.ASM`:

Replace line 13 with:

```
LBNE    PLAY20          ;   yes ; NAB: BNE to LBNE
```

On line 17, insert the follwing code after the `PLAY10` label:

```
        ; NAB - check for joystick input.

        ; Joystick button.
        LDA     65280
        ANDA    #1
        CMPA    #0
        BNE     NOBUTTONPRESS
        
        ; Attack right.
        LDU     #PRHAND
        JSR     ATTACKRIGHT
        JMP     NOMOVE

NOBUTTONPRESS

        JSR     [$A00A]         ; BASIC joystick reading routine.

        LDA     $015B           ; Right joystick y-axis
        CMPA    #10
        BHI     NOMOVEFORWARD

        ; Move forward
        JSR     PMOVEFORWARD
        JMP     NOMOVE

NOMOVEFORWARD
        CMPA    #50
        BLO     NOMOVEBACKWARD

        ; Move backward
        JSR     PMOVEBACKWARD
        JMP     NOMOVE

NOMOVEBACKWARD
        LDA     $015A           ; Right joystick x-axis
        CMPA    #10
        BHI     NOMOVELEFT

        ; Move left
        LDB     PDIR
        JSR     PMOVELEFT
        JMP     NOMOVE

NOMOVELEFT
        CMPA    #50
        BLO     NOMOVE

        ; Move right
        LDB     PDIR
        JSR     PMOVERIGHT

NOMOVE
        ; NAB - END check for joystick input.
```

Replace line 47 with:

```
LBRA    PLAY10         ;loop ; NAB - BRA to LBRA
```

- In `HUPDAT.ASM`:

This is optional, if you want infinte health.  After line 40, add:

```
        CLRA                    ; NAB - IDDQD
        CLRB
        STD PDAM
```

- In `ONCE.ASM`:

After line 284, add:

```
        CLR     TEMP1
```

This is optional, if you want infinite power.  After line 288, add:

```
        LDA     #$7F            ; NAB - Max power.
        STA     PPOW
```

- In `PATTK.ASM`:

After line 11, add this label:

```
ATTACKRIGHT
```

- In `PTURN.ASM`:

After line 20, add the label:

```
PMOVELEFT
```

After line 29, add the label:

```
PMOVERIGHT
```

After line 153, add the label:

```
PMOVEFORWARD
```

After line 166, add the label:

```
PMOVEBACKWARD
```

- In `SOUNDS.ASM`:

On line 16, insert the following after the `SOUNDI` label:

```
; NAB - sound back on (reading joystick turns it off)
        STA     TEMP1
        LDA     $FF23
        ORA     #8
        STA     $FF23
        LDA     TEMP1
```

Replace line 198 with:

```
SNRAT1  LBSR     SNOISE          ;get a random noise value ; NAB - L
```

Replace line 279 with:

```
SNWSH2  LBSR     SNZNVA          ;get some noise ; NAB - L
```

Replace line 285 with:

```
SNWSH1  LBSR     SNENVN          ;decompression subroutine ; NAB - L
```

On line 309, after the `SNOUT` label, insert:

```
        ; NAB - sound back on (reading joystick turns it off)
        STA     TEMP1
        LDA     $FF23
        ORA     #8
        STA     $FF23
        LDA     TEMP1
```

On line 320, insert after the `SNOISE` label:

```
        ; NAB - sound back on (reading joystick turns it off)
        STA     TEMP1
        LDA     $FF23
        ORA     #8
        STA     $FF23
        LDA     TEMP1
```

Replace lines 411-412 with:

```
        LBLS     SNCLK4          ; ; NAB - L
        LBRA     SNOUT           ; ; NAB - L
```

## About the Author

[Nick A. Bild, MS](https://nickbild79.firebaseapp.com/#!/)
