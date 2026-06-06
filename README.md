# Unified-Purge-Bucket
An all in one nozzle cleaning system for the Voron 2.4 

This project was inspired by the Decontaminator Purge Bucket & Nozzle Scrubber (Which can be found here: https://github.com/VoronDesign/VoronUsers/tree/main/orphaned_mods/edwardyeeks/Decontaminator_Purge_Bucket_&_Nozzle_Scrubber). This project attemps to pick up where the Decontaminator Purge Bucket & Nozzle Scrubber left off. 


The Unified Purge Bucket is comprised of two parts, the cleaning station and the nozzle cleaning macro. Here are the updates for both: 

The cleaning station
 - The bucket now contains the nozzle brush. This makes cleaning the brush much easier.
 - The cleaning station is located right next to the z-probe, making nozzle cleaning before a print faster.
 - The purge bucket covers the bed wires, which both keeps the printer cleaning, but also looks much neater too!
 - Sheet stops are intergrated into the bucket mount. 

Macro 
 - The clean_nozzle macro can now run diring a print, and resume printing after the cleaning is done. 
 - Fixed error that is thrown when clean_nozzle is run before homing. Now the macro automatically homes all (G28) before cleaning, if needed.

Required Hardware: 
- 4x M3 t-nuts
- 4x M3x6mm bolts
- 4x M2x10 self-tapping screws
- 8x 6x3mm neodymium magnets

Using the macro: 
It is recommended to place the nozzle_scrub macro in another file to keep the main printer.cfg clean. 

Add this line to your printer.cfg file 
``` jinja2
[include nozzle_scrub.cfg]
```

nozzle_scrub.cfg file: 
``` jinja2
[gcode_macro clean_nozzle]
## Version 0.01 beta

# If you are putting your purge bucket at the rear left of the bed as per default installation, enable True on your location_bucket_rear 
# variable. If you want to put your purge bucket elsewhere (perhaps the front), then set it to False. See diagrams and description 
# further below on how to set your XY position.
variable_location_bucket_rear:      True

# If you want the purging routine in your bucket enabled, set to True (and vice versa).
variable_enable_purge:              True 

# These parameters define your filament purging. The retract variable is used to retract right after purging to prevent unnecessary
# oozing. Some filament are particularly oozy and may continue to ooze out of the nozzle for a second or two after retracting. The
# ooze dwell variable makes allowance for this. Update as necessary. If you decided to not enable purge, you can ignore this section.
variable_purge_len:             4	         ; Amount of filament, in mm, to purge.
variable_purge_spd:           150	         ; Speed, in mm/min, of the purge.
variable_purge_temp_min:      240	         ; Minimum nozzle temperature to permit a purge. Otherwise, purge will not occur.
variable_purge_ret:             8            ; Retract length, in mm, after purging to prevent slight oozing. Adjust as necessary.
variable_ooze_dwell:            2            ; Dwell/wait time, in seconds, after purging and retracting.

# Adjust this so that your nozzle scrubs within the brush. Currently defaulted to be a lot higher for safety. Be careful not to go too low!
variable_brush_top:            10  ## << Change this value to match your brush height!! 

# Additional lift above current print height during travel
variable_safe_z_lift: 2

# These parameters define your scrubbing, travel speeds, safe z clearance and how many times you want to wipe. Update as necessary. Wipe 
# direction is randomized based off whether the left or right bucket is randomly selected in the purge & scrubbing routine.
variable_clearance_z:           5	         ; When traveling, but not cleaning, the clearance along the z-axis between nozzle and brush.
variable_wipe_qty:              3	         ; Number of complete (A complete wipe: left, right, left OR right, left, right) wipes.
variable_prep_spd_xy:        3000	         ; Travel (not cleaning) speed along x and y-axis in mm/min.
variable_prep_spd_z:         1500	         ; Travel (not cleaning) speed along z axis in mm/min.
variable_wipe_spd_xy:        5000	         ; Nozzle wipe speed in mm/min.


# These parameters define the size of the brush. Update as necessary. A visual reference is provided below. Note that orientation of 
# parameters remain the same whether bucket is at rear or front.
# 
#                  ←   brush_width   →                            
#                   _________________                                                          ↑
#                  |                 |  ↑                     If you chose location_bucket_rear = True, Y position is acquired
#  brush_start (x) |                 | brush_depth            from your stepper_y position_max. Adjust your brush physically in
#                  |_________________|  ↓                               Y so that the nozzle scrubs within the brush.       
#                          (y)                                                                 ↓
#                      brush_front
# __________________________________________________________
#                     PRINTER FRONT
# 
# 

## For V2.4 350mm build:
variable_brush_start:     155 ## For modded bucket, stock 75

# This value is defaulted from brush location in CAD (rear left). Change if your brush width is different.
variable_brush_width:          52	

## These are only used if location_bucket_rear is False. You specify a custom location in y axis for your brush - see diagram above. ##
variable_brush_front:       0          
variable_brush_depth:       0          


# These parameters define the size of your purge bucket. Update as necessary. If you decided to not enable purge, you can ignore 
# this section. A visual reference is provided below. Note that orientation of parameters remain the same whether bucket is at rear 
# or front.
# 
#                                     bucket_gap
#                                      ← ---- →
#                     __________________________________________
#                    |                 |      |                 |      
#                    |                 |      |                 |
#  bucket_start (x)  |                 |______|                 |
#                    |                 |      |                 |
#                    |                 |      |                 |
#                    |_________________|. . . |_________________|
#                     ← ------------- →        ← ------------- →
#                     bucket_left_width        bucket_right_width
#  _______________________________________________________________________________________
#                                    PRINTER FRONT
# 

## For V2.4 350mm build: 
variable_bucket_left_width:    130 ## modded for the new bucket, stock is 92   

# These values are defaulted from bucket geometry in CAD (rear left location). Change only if you're using a custom bucket.
variable_bucket_right_width:   40            
variable_bucket_gap:           22	

# For V1.8, you may need to measure where your bucket start is and input into bucket_start. Otherwise, a value of 0 is for a default
# installation of purge bucket at rear left.
variable_bucket_start:          50 ## Modded, stock 0

# Which bucket should the purge be done? -1 = random, 0 = left, 1 = right
variable_bucket_purge:          0  ## the left side is larger

# Which bucket should we park the nozzle at the end of purge? 0 = left, 1 = right
variable_bucket_park:           1


###############################################################################################################################################
###############################################################################################################################################

### From here on, unless you know what you're doing, it's recommended not to change anything. Feel free to peruse the code and reach out to me
### (edwardyeeks#6042) on Discord if you spot any problems!

###############################################################################################################################################
###############################################################################################################################################

gcode:

      # check if printer is currently printing, or paused. this allows the printhead to return to the current position. 
      {% set is_printing = printer.print_stats.state in ["printing", "paused"] %}

      ## Define z position data
      {% set current_z = printer.toolhead.position.z %}
      {% set max_z = printer.toolhead.axis_maximum.z|float %}
      {% set travel_z = [current_z + safe_z_lift, max_z] | min %}


       # First, check if the axes are homed, if not, home all. 
       {% if printer.toolhead.homed_axes != "xyz" %}
         M117 Homing...
         G28
      {% endif %}

      ## Save the gcode state in this macro instance.
      SAVE_GCODE_STATE NAME=clean_nozzle
  
      ## Set to absolute positioning.
      G90

      ## Only save the toolhead position if printing 
      {% if is_printing %}
          ## Save current toolhead position.
          {% set restore_x = printer.toolhead.position.x %}
          {% set restore_y = printer.toolhead.position.y %}
          {% set restore_z = printer.toolhead.position.z %}
      
          ## Calculate safe travel height
          {% set max_z = printer.toolhead.axis_maximum.z|float %}
          {% set travel_z = [restore_z + safe_z_lift, max_z] | min %}
      {% endif %}


      ## Grab max position of Y-axis from config to use in setting a fixed y position for location_bucket_rear = True.
      {% set Ry = printer.configfile.config["stepper_y"]["position_max"]|float %}

      ## Check if user enabled purge option or not.
      {% if enable_purge %}

         {% if bucket_purge < 0 %}
            ### Randomly select left or right bin for purge. 0 = left, 1 = right
            {% set _bucket_pos = (range(2) | random) %}
         {% else %}
            {% set _bucket_pos = bucket_purge %}
         {% endif %}

         ### Raise Z for travel.
         G1 Z{travel_z} F{prep_spd_z}

         ### Check if user chose to use rear location.
         {% if location_bucket_rear %}
            G1 Y{Ry} F{prep_spd_xy}
         {% else %}
            G1 Y{brush_front + (brush_depth / 2)} F{prep_spd_xy}
         {% endif %}

         ### Position for purge. Randomly selects middle of left or right bucket. It references from the middle of the left bucket.
         G1 X{bucket_start + (bucket_left_width / (2 - _bucket_pos)) + (_bucket_pos * bucket_gap) + (_bucket_pos * (bucket_right_width / 2))}

         ### Perform purge if the temp is up to min temp. If not, it will skip and continue executing rest of macro. Small retract after
         ### purging to minimize any persistent oozing at 5x purge_spd. G4 dwell is in milliseconds, hence * 1000 in formula.
         {% if printer.extruder.temperature >= purge_temp_min %}
            M83      ; relative mode
            G1 E{purge_len} F{purge_spd}
            G1 E-{purge_ret} F{purge_spd * 5}
            G4 P{ooze_dwell * 1000}
            G92 E0   ; reset extruder
         {% endif %}

      {% endif %}
   
      ## Position for wipe. Either left or right of brush based off _bucket_pos to avoid unnecessary travel.
      G1 Z{travel_z} F{prep_spd_z}
      G1 X{brush_start + (brush_width * _bucket_pos)} F{prep_spd_xy}

      ## Check if user chose to use rear location.
      {% if location_bucket_rear %}
         G1 Y{Ry}
      {% else %}
         G1 Y{brush_front + (brush_depth / 2)}
      {% endif %}

      ## Move nozzle down into brush.
      G1 Z{brush_top} F{prep_spd_z}
   
      ## Perform wipe. Wipe direction based off _bucket_pos for cool random scrubby routine.
      {% for wipes in range(1, (wipe_qty + 1)) %}
         G1 X{brush_start + (brush_width * (1 - _bucket_pos))} F{wipe_spd_xy}
         G1 X{brush_start + (brush_width * _bucket_pos)} F{wipe_spd_xy}
      {% endfor %}
   
      ## Clear from area.
      M117 Cleaned!
      G1 Z{travel_z} F{prep_spd_z}
      G1 X{bucket_start + (bucket_left_width * (1 + (3 * bucket_park)) / 4) + (bucket_park * bucket_gap) + (bucket_park * (bucket_right_width / 2))} F{prep_spd_xy} #bugfix for right side mounted buckets

      
      ## Only return toolhead if printing, otherwise, park above purge bucket. 
      {% if is_printing %}
      
          ## Return above original print position safely
          G1 Z{travel_z} F{prep_spd_z}
      
          ## Return to original XY while elevated
          G1 X{restore_x} Y{restore_y} F{prep_spd_xy}
      
          ## Descend near print height
          G1 Z{restore_z + 0.6} F{prep_spd_z}
      
          ## Slow final approach
          G1 Z{restore_z} F{prep_spd_z / 3}
      
      {% endif %}
      

      ## Restore the gcode state to how it was before the macro.
      RESTORE_GCODE_STATE NAME=clean_nozzle
```


We are currently in open beta! I have tested this system for many weeks, but I want to make it even better. Please let me know how it works for you! 
