# Sovol-SV08-Macros
Sovol SV08 - Optimized Macro.cfg &amp; printer.cfg / Orca G-Code &amp; Settings

HOW HARD IS IT TO IMPLEMENT?

Basically it's a work of 10 minutes. 
Simply copy/paste the code from Macro.cfg and printer.cfg over your original cfgs. Additionally paste your serial numbers from old printer.cfg to new printer.cfg. Optimized Orca Slicer settings are optional EXCEPT Machine start G-Code and 0.6mm nozzle setting in Orca Slicer settings AND printer.cfg if 0.4mm nozzle installed!
Do STEP 1 TO STEP 5 for best experience.

> [!CAUTION]
- Stock Sovol hardware required (with 0.6mm  nozzle). For  0.4mm nozzle minor code adjustment needed in printer.cfg. See below on the end of this article.
- Stock Sovol SV08 Firmware 2.4.6, this might not work with older and newer firmware in the future!
- You will have to use all files / settings together for optimal function ( Orca machine G-Code (!) / printer.cfg (Mainsail Klipper) / Macro.cfg (Mainsail Klipper)
- You will have to paste your serial number to the config.cfg file! See printer.cfg section! Do not forget!
- Do not implement if you don't know what you are doing (knowledge about parameters in Klipper configs, how to troubleshoot,…. required)
- Make backups of your current settings / configs!
- This may work with PLA too. You have to change temp settings to PLA temps in Macro.cfg (temperature values in [gcode_macro _global_var])
- You will have to uncomment some gcode options if hardware installed (like Neopixel Nozzle LED, chamber fan ...) -> Search for "uncomment" to see and unlock the options. It is already preconfigured but not active.
- For PETG prints use the smooth side of the PEI plate. PETG sticks too much on coarse side.
- I recommend to upgrade your SV08 with this mod too, to avoid nozzle cleaning blobs and other residuals getting into the Z-belt mechanics: https://www.printables.com/model/1257347-sovol-sv08-z-belt-cover
- To fix your bed (TACO SHAPE) do this fix: https://www.printables.com/model/1073040-sovol-sv08-taco-bed-fix
Heat soaking and other voodoo is no solution, this taco bed fix (metal spacers in the center between bed and printer base plate) eliminates the root cause for bad first layers.
- If you are using a 12V fan replacement for the mainboard with a buck converter, then be sure the fan spins up again after shutting off when temperature is low / idle.
- Use at own risk, this is WORK IN PROGRESS.

**OPTIMIZATIONS Changelog (firmware 2.4.6 required!):**

Full changelog with previous versions see download section

Version 20 (08.Nov.2025)
- Fixed a bug in printer.cfg. Please check code with original cfg files to avoid bugs.

**STEP 1: ORCA SETTINGS G-Code:**
Must be used with optimized printer.cfg and Macro.cfg for correct print start (no purge line and blob on bed) and avoid bed scratching in certain cases!

Orca Machine start G-Code:
```
START_PRINT
G90; Absolute positioning
G1 Z3.5 F400; move safe z-height
G1 X0 Y0 F3000; move to zero position
G1 Z0.0 F400; preheat nozzle sealed on plate
M104 S[nozzle_temperature_initial_layer]; set extruder temp
M190 S[bed_temperature_initial_layer_single]; wait for bed temp
M109 S[nozzle_temperature_initial_layer]; wait for extruder temp
G1 Z0.3 F400; add nozzle height adjust
M400; wait finshed
G1 Y4.0 F7000; wipe
M400; wait finshed
G1 Z0.5 F600; add nozzle raise
M400; wait finshed
G91; set position relative
M83; set extruder relative
M400; wait until finished
```

**STEP 2: New code for Klipper  (Macro.cfg & printer.cfg)**

Attention: Be sure to have backups of original Macro.cfg and printer.cfg with your serial numbers!

You can compare your original code with this new code here to see all changes (paste original code in left column, new code in right column): https://editor.mergely.com

Macro.cfg

paste over old code in Orca slicer Device Tab -> Machine -> Macro.cfg (Save & Reboot)

Be sure to have full code from:
```
[gcode_macro BEEP]
...
```
to
```
...
#-----------MOD END - LED MACROS TO SET LCD COLORS------------------#
```

printer.cfg (full code in file in download section)

paste over old code in Orca Device Tab -> Machine -> printer.cfg (Save & Reboot)

Be sure the tabs stops are still  inserted in section  [quad_gantry_level] coordinates.

> [!CAUTION]
> YOU MUST PASTE YOUR PRINTER/KLIPPER SERIAL IN FOLLOWING SECTIONS:

```
....

[mcu] # NEED YOUR SERIAL PASTED HERE
serial: PASTE SERIAL HERE!
restart_method: command

[mcu extra_mcu] # NEED YOUR SERIAL PASTED HERE
serial: PASTE SERIAL HERE!
restart_method: command

......
......
```
Check if your serial numbers are adopted in printer.cfg as described above!

IF YOU WANT TO KEEP YOUR OLD Z-OFFSET AND BED MESH VALUES THEN LEAVE YOUR ORIGINAL LAST PART IN GREEN LETTERS STARTING WITH SAVE_CONFIG 
```
......

#*# <---------------------- SAVE_CONFIG ---------------------->
#*# DO NOT EDIT THIS BLOCK OR BELOW. The contents are auto-generated.
#*#
#*# [probe]
#*# z_offset = 1.0
#*#
#*# [input_shaper]
#*# shaper_type_x = zv
#*# shaper_freq_x = 60.0
#*# shaper_type_y = mzv
#*# shaper_freq_y = 42.8
#*#
#*# [extruder]
#*# control = pid
#*# pid_kp = 33.838
#*# pid_ki = 5.223
#*# pid_kd = 47.752
#*#
#*# [heater_bed]
#*# control = pid
#*# pid_kp = 73.571
#*# pid_ki = 1.820
#*# pid_kd = 783.849
```

There is a Settings Override Tab in Orca: Set Retraction also to 0.6mm there or you will get small holes in your print surface from time to time!

**STEP 4: DO CALIBRATIONS: **

Reboot machine
Set 0,4mm nozzle in printer.cfg and Orca Slicer if no 0,6mm nozzle installed
Be sure your serial numbers are pasted from old printer.cfg in new printer.cfg
Be sure temperatures for nozzle and bed are adjusted in Orca and Macro.cfg (see global_variables). Default are PETG temperatures. For PLA lower temp values required. 
uncomment hardware upgrade code in Macro.cfg and printer.cfg for Neopixel nozzle LED and chamber fan if installed
(optional PID Tuning (Menu in LCD display))
Calibrate Z-offset (Menu in LCD display) - Important!
Quad Gantry Level  (Menu in LCD display) - Important
Bed Mesh (Menu in LCD display) - Important
Auto-Calibrate (= Input Shaping Menu in LCD display) - Important
Belt Test - Important
The individual printer calibration settings will be stored in modified printer.cfg (section SAVE_CONFIG)

**STEP 5: Test printer and all fans in Mainsail with sliders, e.g. test prints,….**

==============================

**Why 0.6mm nozzle and PETG as filament?**

- I installed the new Sovol upgrade nozzle with 0.6mm diameter for even faster prints and more reliability (no clogging). The maximum volumetric flow increased from 15mm3/s to 26mm3/s with PETG! It's possible with Sovol SV08.
Orca slicer can compensate bigger nozzles with quality settings magic (Adaptive layer height button, Smooth button, fuzzy skin, ....), except very fine details.

- Why PETG? I can put my prints in the dish washer at 70°C or use outside without deformation. I use only PETG from Geetech, this never fails. Therefore higher temps in Orca settings.

Therefore these settings are optimized for PETG and 0.6mm nozzle, but it can be adapted for PLA and standard 0.4mm nozzle in Orca / printer.cfg again. See below.

==============================

**OPTIONAL for 0.4mm nozzle:**

(It is not recommended to print PETG with standard 0,4mm non-upgrade Sovol nozzle without the nozzle screw! It may damage your bed and toolhead! Replace with upgrade nozzle or 3rd party upgrade by e.g. Microswiss)

Use your own 0.4mm Orca presets except modify Orca machine G-Code and set nozzle to 0.4mm in Extruder 1 tab (Nozzle diameter: 0.6 mm).
In Addition change this line in printer.cfg:
```
nozzle_diameter: 0.600 # 0.400 MOD for 0.6mm nozzle install
```
to
```
nozzle_diameter: 0.400
```
=============================
