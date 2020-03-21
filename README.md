# PrusaSlicer-DualExtruder
How to make dual extruders work with PrusaSlicer.
* Skip the crap and start reading at; *"OKAY, problem - solution:"*

Having a dual extruder printer (Customized Marlin FW) and changing from Simplify3D to PrusaSlicer needed some work-arounds.
Simplify3D has a wonderful G-Code-viewer which I have not seen elsewhere! (Not tried many slicers though.)
PrusaSlicer "sucks" in the way you cannot see individual moves, only full layers! I use OctoPrint to view the result. But nothing comes near Simplify3D. And it's also tideous to slice - export - view in OctoPrint (which feels very basic).
The reason for changing is that the way Simplify3D stores the settings in Windows registry makes it HELL to backup, copy, or modify.
The plain textfiles and settings for Printer, Filament, Printing, are so much easier to handle in PrusaSlicer!

So...
Since Prusa either focus on single extruder, or single extruder with several filaments going back and forth, Prusa doesn't quickly fix problems for other printers.
Why should they? Well, maybe because they used open source code in the first place... well well... It is good they have support for other brand of printers... and yet again, their printers are based on Marlin FW :)

*OKAY, problem - solution:*
Problem: 
* https://github.com/prusa3d/PrusaSlicer/issues/2710#issue-476282321
* When using dual extruders, the 2nd extruder doesn't get Retract-UnRetract gcode as it should. First is't oozing a lot due to missing Retract. Then it's missing the UnRetract for the first toolchange. Thus giving a severe underextrution for quite some time, i. e. things won't get printed at all.
Solution:
* Having a post processing script that inserts a lot of extruder priming the 1st time the 2nd extruder is activated.

She post processing is done automatically by PrusaSlicer. The code is in Python (and this is not a tutorial to get Python running).
The code gets parameters from the *custom gcode sections* in PrusaSlicer.



Initial G-Code looks like this, the comments in *=== Post process vars ===* are important:
- - - - - - -
>I think you should use an

>; Purge nozzle 1 or 1+2

>;------------------------

> ; Both extruders are used.

>  ; Initiate Standby tool.

>  T1 ; Standby tool.
>  G92 E0 ; Zero extruders
>  G1 E20 F600  ; Purge nozzle, use same speed as for deretraction.
>  G1 E-5 F1800  ; Retract the tool not to be used at the moment.
>  ; === Post process vars ===>>>
>  ; TOOLS 2
>  ; FIRST_TOOLCHANGE_EXTRA_RESTART 30 mm, manual setting here.
>  ; RETRACT_LENGTH_TOOLCHANGE 5 
>  ; RETRACT_RESTART_EXTRA_TOOLCHANGE 0.2 
>  ; RETRACT_SPEED 1800 
>  ; DERETRACT_SPEED 600 
>  ; <<<=== Post process vars ===
>
>T0 ; Active tool.
>G92 E0 ; Zero extruders
>G1 E20 F600  ; Purge nozzle, use same speed as for deretraction.
- - - - - - -



And when the very first tool change occurs, the head is moved off table and then purged. You can purge anywhere you like. I started with a wipe-tower, but why: lets purge outside the bed.
- - - - - - -

; -------------------------------------->>>
; Custom G-code: Printer - Tool change - START
; INITIAL_TOOL 0
; NEXT_EXTRUDER 1
T1 ; Do the one time UnRetract for the tool that has not been used until now.
G0 X-7 Y30 F5000 ; Move to wait position off table and purge next tool.
G92 E0 ; Zero extruders
G1 E35.2 F600 ; UnRetract
; LAYER_NUM 2
; Custom G-code: Printer - Tool change - END
; <<<--------------------------------------


