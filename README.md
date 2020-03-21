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
* When using dual extruders, the 2nd extruder doesn't get Retract-UnRetract gcode as it should. It's missed for the first toolchange. Thus giving a severe underextrution for quite some time, i. e. things won't get printed at all.
Solution:
* Having a post processing script that inserts a lot of extruder priming the 1st time the 2nd extruder is activated.
