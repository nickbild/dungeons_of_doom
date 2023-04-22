# Dungeons of Doom

Dungeons of Doom is a hack of the TRS-80 Color Computer classic Dungeons of Daggorath.  I added in some characters from Doom and enabled joystick-based controls, among other things.

![](https://raw.githubusercontent.com/nickbild/dungeons_of_doom/main/media/capture2.png)

## About the Game

Dungeons of Daggorath was a very popular first-person RPG in the early 1980s.  It was compatible with the Tandy TRS-80 Color Computer 1/2/3.

It was a very early three-dimensional game that allowed the player to explore a dungeon, and battle monsters via a text based interface.  That interface is used to move around, fight, pick up or use items, and much more.  However, the vast majority of the time, the commands entered revolve around moving and attacking.

For these common actions, the text-based interface can be a bit slow and clunky, so I modified the source code to enable the right joystick.  Moving the joystick moves the player around the dungeon, just as would be expected.  The button triggers the command `ATTACK RIGHT`.

Being a first-person game, I could not resist the temptation to insert a few Doom characters in place of some of the originals.  The CoCo may not be able to play Doom, but it kinda-sorta can do something like it.

I also gave the player unlimited power and health, which is great for exploring and testing, but that boost can be left out for those that want the challenge.

After assembly, the binary can be burned to a ROM chip mapped into the cartridge port, or should also work with any decent emulator.  I assembled the source code with [LWASM](http://www.lwtools.ca/manual/c62.html).

Speaking of source code, the former CEO of DynaMicro, Inc. very graciously released the source code for public distribution, but only in its original form.  Since I have modified it, I cannot distribute my work.  However, one can still recreate Dungeons of Doom without too much trouble by:

1) Downloading the [original 6809 source code](https://github.com/MichaelSpencerJr/DungeonsOfDaggorath).
2) Making the modifications I have [listed below](#modifications-to-the-original-source-code).
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

- In `D3.ASM`:

Replace the spider vector list with:

```
;       Body
SPIDER  SVORG   40,100
        SVECT   40,105
        SVECT   42,115
        SVECT   52,125
        SVECT   60,128
        SVECT   68,125
        SVECT   78,115
        SVECT   80,105
        SVECT   80,95
        SVECT   78,90
        SVECT   68,80
        SVECT   60,77
        SVECT   52,80
        SVECT   42,90
        SVECT   40,100
        SVNEW

;       Eye
        SVORG   50,101
        SVECT   52,106
        SVECT   56,106
        SVECT   58,101
        SVECT   56,96
        SVECT   52,96
        SVECT   50,101
        SVNEW

;       Pupil
        SVORG   53,99
        SVECT   53,103
        SVECT   55,103
        SVECT   55,99
        SVECT   53,99
        SVNEW

;       Mouth
        SVORG   64,90
        SVECT   64,95
        SVECT   64,101
        SVECT   64,105
        SVECT   64,112
        SVECT   69,114
        SVECT   73,110
        SVECT   74,101
        SVECT   73,92
        SVECT   69,88
        SVECT   64,90
        SVNEW

;       Teeth
        SVORG   65,95
        SVECT   70,97
        SVECT   65,99
        SVNEW
        SVORG   65,103
        SVECT   70,105
        SVECT   65,107
        SVNEW
        SVORG   71,99
        SVECT   66,101
        SVECT   71,103
        SVNEW

;       Horns
        SVORG   44,118
        SVECT   40,124
        SVECT   35,127
        SVECT   45,127
        SVECT   50,123
        SVNEW

        SVORG   44,87
        SVECT   40,81
        SVECT   35,78
        SVECT   45,78
        SVECT   50,82
        SVNEW

        SVORG   40,108
        SVECT   35,111
        SVECT   41,114
        SVNEW

        SVORG   40,97
        SVECT   35,94
        SVECT   41,91
        SVEND

; End of Spider vector list
```

- In `D4.ASM`:

Replace the viper vector list with:

```
VIPER
;       Head
        SVORG   55,120
        SVECT   55,130
        SVECT   55,140
        SVECT   62,147
        SVECT   69,140
        SVECT   75,140
        SVECT   83,140
        SVECT   83,130
        SVECT   83,120
        SVECT   75,120
        SVECT   69,120
        SVECT   62,113
        SVECT   55,120
        SVNEW

;       Eyes
        SVORG   62,118
        SVECT   66,124
        SVECT   66,129
        SVECT   62,123
        SVECT   62,118
        SVNEW

        SVORG   62,142
        SVECT   66,136
        SVECT   66,131
        SVECT   62,137
        SVECT   62,142
        SVNEW

;       Mouth
        SVORG   70,122
        SVECT   70,130
        SVECT   70,138
        SVECT   78,138
        SVECT   78,130
        SVECT   78,122
        SVECT   70,122
        SVNEW

        SVORG   70,124
        SVECT   72,124
        SVECT   72,130
        SVECT   72,136
        SVECT   70,136
        SVNEW

        SVORG   78,124
        SVECT   76,124
        SVECT   76,130
        SVECT   76,136
        SVECT   78,136
        SVNEW

        ; R
        SVORG   68,140
        SVECT   73,145
        SVECT   78,150
        SVECT   85,152
        SVECT   88,153
        SVECT   98,155
        SVECT   98,150
        SVECT   88,145
        SVECT   88,140
        SVECT   98,139
        SVECT   108,138
        SVECT   118,136
        SVECT   124,135
        SVNEW

        ; L
        SVORG   68,120
        SVECT   73,115
        SVECT   78,110
        SVECT   85,108
        SVECT   88,107
        SVECT   98,105
        SVECT   98,110
        SVECT   88,115
        SVECT   88,120
        SVECT   98,121
        SVECT   108,122
        SVECT   118,124
        SVECT   124,125
        SVNEW

        ; Legs
        SVORG   120,128
        SVECT   114,129
        SVECT   104,129
        SVECT   94,128
        SVECT   94,131
        SVECT   104,132
        SVECT   114,132
        SVECT   120,133
        SVNEW

        ; Horns
        SVORG   74,146
        SVECT   68,148
        SVECT   77,149
        SVNEW

        SVORG   74,114
        SVECT   68,112
        SVECT   77,111

        SVEND
```

## About the Author

[Nick A. Bild, MS](https://nickbild79.firebaseapp.com/#!/)
