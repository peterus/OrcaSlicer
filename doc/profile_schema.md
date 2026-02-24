# OrcaSlicer Profile Schema Documentation

> **Generated** summary of all JSON profile structures used in OrcaSlicer.  
> Source: `resources/profiles/` — analyzed across all manufacturers  
> (1031 machine profiles, 5140 filament profiles, 2920 process profiles).  
> Types sourced from `src/libslic3r/PrintConfig.cpp`.

---

## Overview

OrcaSlicer stores printer configuration in three distinct profile types, each saved as a JSON file:

| Profile Type | Directory | Purpose |
|---|---|---|
| **Machine** (`"type": "machine"`) | `<Vendor>/machine/` | Printer hardware capabilities, motion limits, G-code flavour, start/end G-code |
| **Filament** (`"type": "filament"`) | `<Vendor>/filament/` | Material properties, temperatures, fan/cooling settings, retraction per-filament |
| **Process** (`"type": "process"`) | `<Vendor>/process/` | Slicing parameters: layer heights, speeds, infill, supports, walls, etc. |

### Profile File Location

```
resources/profiles/
├── BBL/                        ← Bambu Lab vendor
│   ├── machine/                ← Machine profiles
│   │   ├── fdm_bbl_3dp_001_common.json   ← Base/template (instantiation=false)
│   │   └── Bambu Lab X1 0.4 nozzle.json  ← Selectable profile (instantiation=true)
│   ├── filament/               ← Filament profiles
│   │   └── Bambu PLA Basic @BBL X1C.json
│   └── process/                ← Process profiles
│       └── 0.20mm Standard @BBL X1C.json
├── Creality/
│   └── ...
└── Custom/                     ← User-defined profiles
```

### Inheritance & Metadata

Every profile JSON contains the following **metadata fields** (not slicing parameters):

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Profile type: `"machine"`, `"filament"`, or `"process"` |
| `name` | string | Unique profile name used for display and referencing |
| `inherits` | string | Name of the parent profile; fields not set here inherit from parent |
| `from` | string | Origin: `"system"` (built-in) or `"user"` |
| `setting_id` | string | Internal identifier (e.g. `"GM001"`, `"GF001"`, `"GP001"`) |
| `instantiation` | string | `"true"` = shown in UI; `"false"` = base/template only |
| `description` | string | Human-readable description shown in the UI |
| `compatible_printers` | string[] | Filament/process: machine profile names this profile is compatible with |
| `compatible_printers_condition` | string | Optional expression to compute printer compatibility dynamically |

---

## Data Type Legend


| Type | Meaning | JSON storage example |
|------|---------|---------------------|
| `string` | Text value | `"marlin"` |
| `integer` | Whole number | `"3"` |
| `integer[]` | Array of integers (one per extruder) | `["220", "230"]` |
| `float` | Decimal number | `"0.2"` |
| `float[]` | Array of floats | `["0.4", "0.6"]` |
| `boolean` | True/false (stored as `"0"` or `"1"`) | `"1"` |
| `boolean[]` | Array of booleans | `["0", "1"]` |
| `enum` | String with restricted allowed values | `"marlin"` |
| `enum[]` | Array of enums | `["marlin", "klipper"]` |
| `percent` | Percentage value stored as string with `%` | `"15%"` |
| `percent[]` | Array of percentages | `["110%", "100%"]` |
| `float\|percent` | Either a float or a percentage | `"50"` or `"50%"` |
| `point` | 2-D coordinate `x,y` | `"128x128"` |
| `point[]` | Array of 2-D coordinates | `["0x0", "256x256"]` |
| `point3` | 3-D coordinate `x,y,z` | `"0,0,0"` |
| `point[][]` | Grouped sets of 2-D coordinates | (nested array) |
| `internal` | Internal/computed value, not user-editable | — |

> **Important:** OrcaSlicer stores *all* JSON values as strings (even integers, floats, and booleans).  
> The **Data Type** column reflects the *semantic / logical* type from the C++ source (`src/libslic3r/PrintConfig.cpp`),  
> not the JSON storage representation.  When writing a database importer, parse the string into the declared type.


---

## 1. Machine Profile Fields

Machine profiles describe the **physical printer**: print area, axes, speeds, accelerations,
G-code flavour, hotend geometry, and template G-code sequences.

> For array types (e.g. `integer[]`, `float[]`), the **Examples** column shows sample *element* values
> seen across profiles, not a full array literal.

### Machine Profile Fields

| Field | Data Type | Label | Description | Examples / Enum Values |
|-------|-----------|-------|-------------|------------------------|
| `active_feeder_motor_name` | string | — |  | `mixing_stepper` |
| `adaptive_bed_mesh_margin` | float | Mesh margin | This option determines the additional distance by which the adaptive bed mesh area should be expanded in the XY directions. | `0`, `10`, `5` |
| `apply_top_surface_compensation` | boolean | — |  | `0` |
| `auto_disable_filter_on_overheat` | boolean | — |  | `0` |
| `auto_toolchange_command` | boolean | — |  | `0` |
| `auxiliary_fan` | boolean | Auxiliary part cooling fan | Enable this option if machine has auxiliary part cooling fan. G-code command: M106 P2 S(0-255). | `0`, `1` |
| `bbl_use_printhost` | boolean | Use 3rd-party print host | Allow controlling BambuLab's printer through 3rd party print hosts. | `0` |
| `bed_custom_model` | string | Bed custom model |  | `` |
| `bed_custom_texture` | string | Bed custom texture |  | `` |
| `bed_exclude_area` | point[] | Bed exclude area | Unprintable area in XY plane. For example, X1 Series printers use the front left corner to cut filament during filament change. | `0x0`, `242x0`, `250x0` |
| `bed_mesh_max` | point | Bed mesh max |  | `0,0`, `211,211`, `243,245` |
| `bed_mesh_min` | point | Bed mesh min |  | `-99999,-99999`, `0,0`, `1.5,7` |
| `bed_mesh_probe_distance` | point | Probe point distance | This option sets the preferred distance between probe points (grid size) for the X and Y directions, with the | `0,0`, `22,22`, `37.8,40` |
| `bed_model` | string | — |  | `Snapmaker A250_bed.stl`, `Snapmaker A350_bed.stl`, `Snapmaker Artisan_bed.stl` |
| `bed_shape` | string | — |  | `rectangular` |
| `bed_temperature_formula` | enum | Bed temperature type | This option determines how the bed temperature is set during slicing: based on the temperature of the first filament or the highest temperature of the printed… | `by_first_filament`, `by_highest_temp` |
| `bed_texture` | string | — |  | `Snapmaker A250 Dual_texture.svg`, `Snapmaker A250_texture.svg`, `Snapmaker A350 Dual_texture.svg` |
| `bed_texture_area` | point[] | — |  | `0x-10`, `256x-10` |
| `before_layer_change_gcode` | string | Before layer change G-code | This G-code is inserted at every layer change before the Z lift. |  |
| `best_object_pos` | point | Best object position | Best auto arranging position in range [0,1] w.r.t. bed shape. | `0.3x0.5`, `0.5,0.5`, `0.5x0.5` |
| `box_id` | integer | — |  | `1`, `3` |
| `change_extrusion_role_gcode` | string | Change extrusion role G-code | This G-code is inserted when the extrusion role is changed. |  |
| `change_filament_gcode` | string | Change filament G-code | This G-code is inserted when filament is changed, including T commands to trigger tool change. |  |
| `cooling_filter_enabled` | boolean | — |  | `0` |
| `cooling_tube_length` | float | Cooling tube length | Length of the cooling tube to limit space for cooling moves inside it. | `0`, `20`, `5` |
| `cooling_tube_retraction` | float | Cooling tube position | Distance of the center-point of the cooling tube from the extruder tip. | `0`, `60`, `90` |
| `creality_flush_time` | float | — |  | `86.0` |
| `default_bed_type` | string | Default bed type | Default bed type for the printer (supports both numeric and string format). | `4`, `Textured PEI Plate` |
| `default_filament_profile` | string[] | Default filament profile | Default filament profile when switching to this machine profile. | `Chuanying Generic ABS`, `Chuanying Generic PLA`, `Generic PLA @MyToolChanger` |
| `default_nozzle_volume_type` | enum[] | — |  | `Standard` |
| `default_print_profile` | string | Default process profile | Default process profile when switching to this machine profile. | `0.20mm Standard @LONGER LK10 (0.2 nozzle)`, `0.20mm Standard @LONGER LK10 (0.6 nozzle)`, `0.20mm Standard @LONGER LK10 Plus (0.2 nozzle)` |
| `deretract_speed_extruder_change` | integer | — |  | `30` |
| `deretraction_speed` | float[] | De-retraction Speed | Speed for reloading filament into the nozzle. Zero means same speed of retraction. | `35`, `40`, `50` |
| `detraction_speed` | integer | — |  | `15`, `25` |
| `disable_m73` | boolean | Disable set remaining print time | Disable generating of the M73: Set remaining print time in the final G-code. | `0`, `1` |
| `emit_machine_limits_to_gcode` | boolean | Emit limits to G-code | If enabled, the machine limits will be emitted to G-code file. This option will be ignored if the G-code flavor is |  |
| `enable_filament_ramming` | boolean | Enable filament ramming | Enable filament ramming | `0`, `1` |
| `enable_long_retraction_when_cut` | integer | — |  | `0`, `1`, `2` |
| `enable_power_loss_recovery` | enum | Power Loss Recovery | Choose how to control power loss recovery. When set to Printer configuration, the slicer will not emit power loss recovery G-code and will leave the printer's… | `printer_configuration`, `enable`, `disable` |
| `enable_pre_heating` | boolean | — |  | `0`, `1` |
| `extra_loading_move` | float | Extra loading distance | When set to zero, the distance the filament is moved from parking position during load | `-2`, `0`, `5` |
| `extruder_clearance_dist_to_rod` | float | — |  | `24`, `33`, `36.5` |
| `extruder_clearance_height_to_lid` | float | Height to lid | Distance of the nozzle tip to the lid. | `140`, `245`, `34` |
| `extruder_clearance_height_to_rod` | float | Height to rod | Distance of the nozzle tip to the lower rod. | `27`, `30`, `34` |
| `extruder_clearance_max_radius` | integer | — |  | `47`, `65`, `68` |
| `extruder_clearance_radius` | float | Radius | Clearance radius around extruder. Used for collision avoidance in by-object printing. | `45`, `47`, `50` |
| `extruder_colour` | string[] | Extruder Color | Only used as a visual help on UI. | ``, `#018001`, `#FCE94F` |
| `extruder_max_nozzle_count` | boolean | — |  | `1` |
| `extruder_offset` | point[] | Extruder offset | If your firmware doesn't handle the extruder displacement you need the G-code | `-20`, `0x0`, `0x1.5` |
| `extruder_printable_area` | point[][] | Extruder printable area |  | `0x0,325x0,325x320,0x320`, `25x0,350x0,350x320,0x320`, `25x0,350x0,350x320,25x320` |
| `extruder_printable_height` | float[] | Extruder printable height | Maximum printable height of this extruder which is limited by mechanism of printer. | `320`, `325` |
| `extruder_type` | enum[] | — |  | `Direct Drive`, `Bowden` |
| `extruder_variant_list` | string[] | — |  | `Direct Drive Standard`, `Direct Drive Standard,Direct Drive High Flow` |
| `extruders_count` | integer | — |  | `1`, `2` |
| `fan_direction` | string | — |  | `left`, `right` |
| `fan_kickstart` | float | Fan kick-start time | Emit a max fan speed command for this amount of seconds before reducing to target speed to kick-start the cooling fan. | `0`, `0.2`, `0.5` |
| `fan_speedup_overhangs` | boolean | Only overhangs | Will only take into account the delay for the cooling of overhangs. | `0`, `1` |
| `fan_speedup_time` | float | Fan speed-up time | Start the fan this number of seconds earlier than its target start time (you can use fractional seconds). | `0`, `0.2`, `0.5` |
| `file_start_gcode` | string | File header G-code | G-code written at the very top of the output file, before any other content. |  |
| `gcode_flavor` | enum | G-code flavor | What kind of G-code the printer is compatible with. | `marlin`, `klipper`, `reprapfirmware`, `repetier`, `teacup`, `makerware`, … (12 total) |
| `grab_length` | float[] | Grab length |  | `0`, `17.4` |
| `group_algo_with_time` | boolean | — |  | `0` |
| `head_wrap_detect_zone` | point[] | — |  | `156x152`, `180x152`, `226x224` |
| `high_current_on_filament_swap` | boolean | High extruder current on filament swap | It may be beneficial to increase the extruder motor current during the filament exchange | `0` |
| `host_type` | enum | Host Type | Orca Slicer can upload G-code files to a printer host. This field must contain | `prusalink`, `prusaconnect`, `octoprint`, `duet`, `flashair`, `astrobox`, … (14 total) |
| `hotend_cooling_rate` | integer | — |  | `2` |
| `hotend_heating_rate` | float | — |  | `2`, `3.6` |
| `is_artillery` | boolean | — |  | `1` |
| `is_custom_defined` | boolean | — |  | `0` |
| `is_support_3mf` | boolean | — |  | `1` |
| `is_support_multi_box` | boolean | — |  | `1` |
| `is_support_timelapse` | boolean | — |  | `1` |
| `layer_change_gcode` | string | Layer change G-code | This G-code is inserted at every layer change after the Z lift. |  |
| `long_retractions_when_cut` | boolean[] | Long retraction when cut (beta) | Experimental feature: Retracting and cutting off the filament at a longer distance during changes to minimize purge. | `0` |
| `machine_LED_light_exist` | boolean | — |  | `1` |
| `machine_end_gcode` | string | End G-code | End G-code when finishing the entire print. |  |
| `machine_hotend_change_time` | boolean | — |  | `0` |
| `machine_load_filament_time` | float | Filament load time | Time to load new filament when switch filament. It's usually applicable for single-extruder multi-material machines. | `0`, `0.5`, `1` |
| `machine_max_acceleration_e` | integer | — |  | `10000`, `20000`, `5000` |
| `machine_max_acceleration_extruding` | float[] | Maximum acceleration for extruding | Maximum acceleration for extruding (M204 P) | `10000`, `500`, `50000` |
| `machine_max_acceleration_retracting` | float[] | Maximum acceleration for retracting | Maximum acceleration for retracting (M204 R) | `1000`, `10000`, `5000` |
| `machine_max_acceleration_travel` | float[] | Maximum acceleration for travel | Maximum acceleration for travel (M204 T), it only applies to Marlin 2. | `10000`, `500`, `50000` |
| `machine_max_acceleration_x` | integer | — |  | `10000`, `500`, `50000` |
| `machine_max_acceleration_y` | integer | — |  | `10000`, `500`, `50000` |
| `machine_max_acceleration_z` | integer | — |  | `100`, `1000`, `300` |
| `machine_max_jerk_e` | float | — |  | `0`, `2.5`, `5` |
| `machine_max_jerk_x` | integer | — |  | `0`, `10`, `8` |
| `machine_max_jerk_y` | integer | — |  | `0`, `10`, `8` |
| `machine_max_jerk_z` | float | — |  | `0`, `0.2`, `0.4` |
| `machine_max_junction_deviation` | float[] | Maximum Junction Deviation | Maximum junction deviation (M205 J, only apply if JD > 0 for Marlin Firmware If your Marlin 2 printer uses Classic Jerk set this value to 0.) | `0`, `0.01` |
| `machine_max_speed_e` | integer | — |  | `100`, `25`, `30` |
| `machine_max_speed_x` | integer | — |  | `2000`, `400`, `500` |
| `machine_max_speed_y` | integer | — |  | `2000`, `300`, `500` |
| `machine_max_speed_z` | integer | — |  | `10`, `12`, `15` |
| `machine_min_extruding_rate` | float[] | Minimum speed for extruding | Minimum speed for extruding (M205 S) | `0`, `0,0` |
| `machine_min_travel_rate` | float[] | Minimum travel speed | Minimum travel speed (M205 T) | `0`, `0,0` |
| `machine_pause_gcode` | string | Pause G-code | This G-code will be used as a code for the pause print. Users can insert pause G-code in the G-code viewer. |  |
| `machine_platform_motion_enable` | boolean | — |  | `0` |
| `machine_prepare_compensation_time` | integer | — |  | `260`, `370` |
| `machine_start_gcode` | string | Start G-code | Start G-code when starting the entire print. |  |
| `machine_switch_extruder_time` | float | — |  | `0`, `5`, `5.6` |
| `machine_tool_change_time` | float | Tool change time | Time taken to switch tools. It's usually applicable for tool changers or multi-tool machines. | `0`, `0.5`, `10` |
| `machine_unload_filament_time` | float | Filament unload time | Time to unload old filament when switch filament. It's usually applicable for single-extruder multi-material machines. | `0`, `0.5`, `1` |
| `manual_filament_change` | boolean | Manual Filament Change | Enable this option to omit the custom Change filament G-code only at the beginning of the print. | `0`, `1` |
| `master_extruder_id` | integer | — |  | `1`, `2` |
| `max_layer_height` | float[] | Max | The highest printable layer height for the extruder. | `0.14`, `0.28`, `0.32` |
| `max_resonance_avoidance_speed` | float | Max | Maximum speed of resonance avoidance. | `120` |
| `min_layer_height` | float[] | Min | The lowest printable layer height for the extruder. | `0.08`, `0.1`, `0.15` |
| `min_resonance_avoidance_speed` | float | Min | Minimum speed of resonance avoidance. | `70` |
| `multi_zone` | boolean | — |  | `1` |
| `multi_zone_number` | integer | — |  | `3` |
| `nozzle_diameter` | float[] | Nozzle diameter | The diameter of nozzle. | `0.2`, `0.4`, `0.6` |
| `nozzle_flush_dataset` | integer[] | — |  | `0`, `1`, `2` |
| `nozzle_height` | float | Nozzle height | The height of nozzle tip. | `2.5`, `4`, `4.2` |
| `nozzle_hrc` | integer | Nozzle HRC | The nozzle's hardness. Zero means no checking for nozzle's hardness during slicing. | `0`, `20`, `55` |
| `nozzle_type` | enum[] | Nozzle type | The metallic material of nozzle. This determines the abrasive resistance of nozzle, and | `undefine`, `hardened_steel`, `stainless_steel`, `tungsten_carbide`, `brass` |
| `nozzle_volume` | float[] | Nozzle volume | Volume of nozzle between the cutter and the end of nozzle. | `0`, `117`, `58.7795` |
| `parking_pos_retraction` | float | Filament parking position | Distance of the extruder tip from the position where the filament is parked | `0`, `22`, `90` |
| `pause_gcode` | string | — |  |  |
| `pellet_modded_printer` | boolean | Pellet Modded Printer | Enable this option if your printer uses pellets instead of filaments. | `0`, `1` |
| `physical_extruder_map` | integer[] | — |  | `0`, `1` |
| `preferred_orientation` | float | Preferred orientation | Automatically orient STL files on the Z axis upon initial import. | `0` |
| `prime_tower_position_type` | string | — |  | `Middle Upper` |
| `print_host` | string | Hostname, IP or URL | Orca Slicer can upload G-code files to a printer host. This field should contain | `192.168.3.24`, `G1OS.local`, `http://10.0.1.200/` |
| `print_host_webui` | string | Device UI | Specify the URL of your device user interface if it's not same as print_host. | ``, `10.0.0.51` |
| `printable_area` | point[] | Printable area |  | `-110x-110`, `0x0`, `110x-110` |
| `printable_height` | float | Printable height | Maximum printable height which is limited by mechanism of printer. | `200`, `250`, `265` |
| `printer_agent` | string | Printer Agent | Select the network agent implementation for printer communication. | `moonraker` |
| `printer_extruder_id` | integer[] | — |  | `1` |
| `printer_extruder_variant` | string[] | — |  | `Direct Drive High Flow`, `Direct Drive Standard` |
| `printer_model` | string | Printer type | Type of the printer. | `Chuanying X1`, `LONGER LK10`, `LONGER LK10 Plus` |
| `printer_notes` | string | Printer notes | You can put your notes regarding the printer here. | ``, `Machine file version 1.0 20251106`, `Machine file version 1.1 20250516` |
| `printer_settings_id` | string | — |  | ``, `Chuanying`, `LONGER` |
| `printer_structure` | enum | Printer structure | The physical arrangement and components of a printing device. | `undefine`, `corexy`, `i3`, `hbot`, `delta` |
| `printer_technology` | enum | Printer technology | Printer technology. | `FFF`, `SLA` |
| `printer_variant` | string | Printer variant | Name of the printer variant. For example, the printer variants may be differentiated by a nozzle diameter. | `0.2`, `0.4`, `0.6` |
| `printhost_apikey` | string | API Key / Password | Orca Slicer can upload G-code files to a printer host. This field should contain | `` |
| `printhost_authorization_type` | enum | Authorization Type |  | `key`, `user` |
| `printhost_cafile` | string | HTTPS CA File | Custom CA certificate file can be specified for HTTPS OctoPrint connections, in crt/pem format. | `` |
| `printhost_password` | string | Password |  | `` |
| `printhost_port` | string | Printer | Name of the printer. | `` |
| `printhost_ssl_ignore_revoke` | boolean | Ignore HTTPS certificate revocation checks | Ignore HTTPS certificate revocation checks in case of missing or offline distribution points. | `0` |
| `printhost_user` | string | User |  | `` |
| `printing_by_object_gcode` | string | Between Object G-code | Insert G-code between objects. This parameter will only come into effect when you print your models object by object. |  |
| `purge_in_prime_tower` | boolean | Purge in prime tower | Purge remaining filament into prime tower. | `0`, `1` |
| `ramming_pressure_advance_value` | float | — |  | `0.02` |
| `remaining_times` | boolean | — |  | `1` |
| `renamed_from` | string | — |  | `ginger G1 3.0 nozzle`, `ginger G1 5.0 nozzle`, `ginger G1 8.0 nozzle` |
| `resonance_avoidance` | boolean | Resonance avoidance | By reducing the speed of the outer wall to avoid the resonance zone of the printer, ringing on the surface of the | `0` |
| `retract_before_wipe` | percent[] | Retract amount before wipe | The length of fast retraction before wipe, relative to retraction length. | `0%`, `100%`, `60%` |
| `retract_length_toolchange` | float[] | Retraction Length (Toolchange) | When retraction is triggered before changing tool, filament is pulled back | `0`, `1`, `17` |
| `retract_lift_above` | float[] | Only lift Z above | If you set this to a positive value, Z lift will only take place above the specified absolute Z. | `0`, `0.3` |
| `retract_lift_below` | float[] | Only lift Z below | If you set this to a positive value, Z lift will only take place below the specified absolute Z. | `0`, `255`, `258` |
| `retract_lift_enforce` | enum[] | On surfaces | Enforce Z-Hop behavior. This setting is impacted by the above settings (Only lift Z above/below). | `All Surfaces`, `Top Only`, `Bottom Only`, `Top and Bottom` |
| `retract_on_top_layer` | boolean | — |  | `0`, `1` |
| `retract_restart_extra` | float[] | Extra length on restart | When the retraction is compensated after the travel move, the extruder will push | `-0.05`, `-0.2`, `0` |
| `retract_restart_extra_toolchange` | float[] | Extra length on restart | When the retraction is compensated after changing tool, the extruder will push | `-0.8`, `-3`, `-5` |
| `retract_when_changing_layer` | boolean[] | Retract when change layer | Force a retraction when changes layer. | `0`, `1` |
| `retraction_distances_when_cut` | float[] | Retraction distance when cut | Experimental feature: Retraction length before cutting off during filament change. | `10`, `18` |
| `retraction_length` | float[] | Retraction Length | Some amount of material in extruder is pulled back to avoid ooze during long travel. | `0.5`, `1`, `1.2` |
| `retraction_minimum_travel` | float[] | Travel distance threshold | Only trigger retraction when the travel distance is longer than this threshold. | `1`, `1.5`, `2` |
| `retraction_speed` | float[] | Retraction Speed | Speed for retracting filament from the nozzle. | `30`, `35`, `45` |
| `scan_first_layer` | boolean | Scan first layer | Enable this to enable the camera on printer to check the quality of first layer. | `0`, `1` |
| `settings_id` | string | — |  | `GM001`, `GM002`, `GM005` |
| `silent_mode` | boolean | Supports silent mode | Whether the machine supports silent mode in which machine use lower acceleration to print. | `0`, `1` |
| `single_extruder_multi_material` | boolean | Single Extruder Multi Material | Use single nozzle to print multi filament. | `0`, `1` |
| `support_air_filtration` | boolean | Support air filtration | Enable this if printer support air filtration G-code command: M106 P3 S(0-255) | `0`, `1` |
| `support_box_temp_control` | boolean | — |  | `0`, `1` |
| `support_chamber_temp_control` | boolean | Support control chamber temperature | This option is enabled if machine support controlling chamber temperature G-code command: M141 S(0-255) | `0`, `1` |
| `support_cooling_filter` | boolean | — |  | `0` |
| `support_multi_bed_types` | boolean | Support multi bed types | Enable this option if you want to use multiple bed types. | `0`, `1` |
| `support_multi_filament` | boolean | — |  | `1` |
| `support_object_skip_flush` | boolean | — |  | `0`, `1` |
| `template_custom_gcode` | string | Custom G-code | This G-code will be used as a custom code. |  |
| `thumbnail_size` | string | — |  | `210x210`, `272x272/PNG`, `380x380` |
| `thumbnails` | string | G-code thumbnails | Picture sizes to be stored into a .gcode and .sl1 / .sl1s files, in the following format: \ | `120x60/PNG`, `140x110`, `16x16` |
| `thumbnails_format` | enum | Format of G-code thumbnails | Format of G-code thumbnails: PNG for best quality, JPG for smallest size, QOI for low memory firmware. | `PNG`, `JPG`, `QOI`, `BTT_TFT`, `COLPIC` |
| `thumbnails_internal` | string | — |  | `512x512/PNG/top` |
| `thumbnails_internal_switch` | boolean | — |  | `1` |
| `time_cost` | float | Time cost | The printer cost per hour. | `0` |
| `time_lapse_gcode` | string | Timelapse G-code |  |  |
| `tool_change_temprature_wait` | boolean | — |  | `0` |
| `toolchange_gcode` | string | — |  |  |
| `travel_slope` | float[] | Traveling angle | Traveling angle for Slope and Spiral Z-hop type. Setting it to 90° results in Normal Lift. | `1`, `2`, `3` |
| `upward_compatible_machine` | string[] | upward compatible machine |  | `BLOCKS Pro S100`, `BLOCKS RD50 V2`, `BLOCKS RF50` |
| `use_active_pellet_feeding` | boolean | — |  | `1` |
| `use_extruder_rotation_volume` | boolean | — |  | `1` |
| `use_firmware_retraction` | boolean | Use firmware retraction | This experimental setting uses G10 and G11 commands to have the firmware | `0`, `1` |
| `use_firmwware_retraction` | boolean | — |  | `0` |
| `use_relative_e_distances` | boolean | Use relative E distances | Relative extrusion is recommended when using \ | `0`, `1` |
| `version` | string | — |  | `1.0.0`, `1.0.0.0`, `2.0.2.0` |
| `wipe` | boolean[] | Wipe while retracting | Move nozzle along the last extrusion path when retracting to clean any leaked material on the nozzle. | `0`, `1` |
| `wipe_distance` | float[] | Wipe Distance | Describe how long the nozzle will move along the last path when retracting. | `1`, `10`, `2` |
| `wrapping_detection_gcode` | string | Clumping detection G-code |  |  |
| `wrapping_exclude_area` | point[] | Probing exclude area of clumping | Probing exclude area of clumping. | `145x310`, `153x256`, `216x256` |
| `z_hop` | float[] | Z-hop height | Whenever the retraction is done, the nozzle is lifted a little to create clearance between nozzle and the print. | `0`, `0.25`, `0.3` |
| `z_hop_types` | enum[] | Z-hop type | Type of Z-hop. | `Auto Lift`, `Normal Lift`, `Slope Lift`, `Spiral Lift` |
| `z_hop_when_prime` | boolean | — |  | `0` |
| `z_lift_type` | string | — |  | `Auto Lift`, `NormalLift` |
| `z_offset` | float | Z offset | This value will be added (or subtracted) from all the Z coordinates | `0` |


---

## 2. Filament Profile Fields

Filament profiles describe **material properties**: printing temperatures, bed temperatures,
fan/cooling behaviour, retraction settings, volumetric speed limits, and per-material G-code.

> For array types (e.g. `integer[]`, `float[]`), the **Examples** column shows sample *element* values
> seen across profiles, not a full array literal.

### Filament Profile Fields

| Field | Data Type | Label | Description | Examples / Enum Values |
|-------|-----------|-------|-------------|------------------------|
| `activate_air_filtration` | boolean[] | Activate air filtration | Activate for better air filtration. G-code command: M106 P3 S(0-255) | `0`, `1` |
| `activate_chamber_temp_control` | boolean[] | Activate temperature control | Enable this option for automated chamber temperature control. | `0`, `1` |
| `adaptive_pressure_advance` | boolean[] | Enable adaptive pressure advance (beta) | With increasing print speeds (and hence increasing volumetric flow through the nozzle) and increasing accelerations, | `0` |
| `adaptive_pressure_advance_bridges` | float[] | Pressure advance for bridges | Pressure advance value for bridges. Set to 0 to disable. | `0` |
| `adaptive_pressure_advance_model` | string[] | Adaptive pressure advance measurements (beta) | Add sets of pressure advance (PA) values, the volumetric flow speeds and accelerations they were measured at, separated by a comma. |  |
| `adaptive_pressure_advance_overhangs` | boolean[] | Enable adaptive pressure advance for overhangs (beta) | Enable adaptive PA for overhangs as well as when flow changes within the same feature. This is an experimental option, | `0` |
| `additional_cooling_fan_speed` | integer[] | Fan speed | Speed of auxiliary part cooling fan. Auxiliary fan will run at this speed during printing except the first several layers | `0`, `100`, `50` |
| `additional_cooling_fan_speed_unseal` | integer | — |  | `0`, `100` |
| `bed_temperature` | integer[] | Bed temperature | Vector of bed temperatures for each extruder/filament. | `100`, `30`, `60` |
| `bed_temperature_difference` | integer | — |  | `10` |
| `bed_temperature_initial_layer` | integer[] | Initial layer bed temperature | Vector of initial layer bed temperatures for each extruder/filament. Provides the same value as first_layer_bed_temperature. | `100`, `35`, `60` |
| `bed_type` | string | — |  | `Cool Plate`, `Hot Plate`, `Textured PEI Plate` |
| `box_temperature` | integer | — |  | `0`, `55`, `60` |
| `box_temperature_range_high` | integer | — |  | `0`, `45`, `55` |
| `box_temperature_range_low` | boolean | — |  | `0` |
| `chamber_temperature` | integer[] | Chamber temperature | For high-temperature materials like ABS, ASA, PC, and PA, a higher chamber temperature can help | `0`, `20`, `35` |
| `chamber_temperatures` | integer | — |  | `0`, `60`, `65` |
| `circle_compensation_speed` | integer | — |  | `200` |
| `close_fan_the_first_x_layers` | integer[] | No cooling for the first | Turn off all cooling fans for the first few layers. | `1`, `2`, `3` |
| `compatible_printers` | string[] | Select printers |  | `LONGER LK10 (0.2 nozzle)`, `LONGER LK10 (0.4 nozzle)`, `LONGER LK10 Plus (0.2 nozzle)` |
| `compatible_printers_condition` | string | Condition | A boolean expression using the configuration values of an active printer profile. | `` |
| `compatible_prints` | string[] | Select profiles |  | `0.25 Benchy @Snapmaker Artisan (0.4 nozzle)`, `0.25 Benchy @Snapmaker J1 (0.4 nozzle)`, `0.25 Benchy @Snapmaker U1 (0.4 nozzle)` |
| `compatible_prints_condition` | string | Condition | A boolean expression using the configuration values of an active print profile. | `` |
| `complete_print_exhaust_fan_speed` | integer[] | Fan speed | Speed of exhaust fan after printing completes. | `0`, `100`, `70` |
| `cool_plate_temp` | integer[] | Bed temperature | Bed temperature for layers except the initial one. | `0`, `35`, `60` |
| `cool_plate_temp_initial_layer` | integer[] | Initial layer bed temperature | Bed temperature of the initial layer. | `0`, `55`, `60` |
| `cool_special_cds_fan_speed` | boolean | — |  | `0` |
| `counter_coef_1` | boolean | — |  | `0` |
| `counter_coef_2` | float | — |  | `0.003`, `0.00545`, `0.02` |
| `counter_coef_3` | float | — |  | `-0.01`, `-0.0202`, `-0.32` |
| `counter_limit_max` | float | — |  | `0.03`, `0.05`, `0.088` |
| `counter_limit_min` | float | — |  | `-0.035`, `-0.05`, `-0.4` |
| `default_filament_colour` | string[] | Default color | Default filament color. | ``, `#008000`, `#FF8000` |
| `description` | string | — |  | ``, `Overture ABS settings from https://overture3d.com/.` |
| `diameter_limit` | integer | — |  | `50` |
| `disable_fan_first_layers` | integer | — |  | `1`, `2`, `3` |
| `dont_slow_down_outer_wall` | boolean[] | Don't slow down outer walls | If enabled, this setting will ensure external perimeters are not slowed down to meet the minimum layer time. | `0`, `1` |
| `during_print_exhaust_fan_speed` | integer[] | Fan speed | Speed of exhaust fan during printing. This speed will override the speed in filament custom G-code. | `0`, `100`, `60` |
| `enable_overhang_bridge_fan` | boolean[] | Force cooling for overhangs and bridges | Enable this option to allow adjustment of the part cooling fan speed for specifically for overhangs, internal and external | `0`, `1` |
| `enable_pressure_advance` | boolean[] | Enable pressure advance | Enable pressure advance, auto calibration result will be overwritten once enabled. | `0`, `1` |
| `enable_volume_fan` | integer | — |  | `40` |
| `eng_plate_temp` | integer[] | Bed temperature | Bed temperature for layers except the initial one. | `0`, `100`, `60` |
| `eng_plate_temp_initial_layer` | integer[] | Initial layer bed temperature | Bed temperature of the initial layer. | `0`, `55`, `60` |
| `external_perimeter_speed` | percent | — |  | `50%` |
| `extruder_rotation_volume` | integer | — |  | `456`, `624` |
| `fan_cooling_layer_time` | float[] | Layer time | Part cooling fan will be enabled for layers of which estimated time is shorter than this value. | `100`, `20`, `30` |
| `fan_max_speed` | float[] | Fan speed | Part cooling fan speed may be increased when auto cooling is enabled. | `100`, `50`, `80` |
| `fan_min_speed` | float[] | Fan speed | Minimum speed for part cooling fan. | `100`, `20`, `35` |
| `filament_adaptive_volumetric_speed` | boolean[] | Adaptive volumetric speed | When enabled, the extrusion flow is limited by the smaller of | `0` |
| `filament_adhesiveness_category` | integer[] | Adhesiveness Category | Filament category. | `100`, `200`, `300` |
| `filament_change_length` | float[] | Filament ramming length | When changing the extruder, it is recommended to extrude a certain length of filament from the original extruder. This helps minimize nozzle oozing. | `10`, `12`, `5` |
| `filament_change_length_nc` | integer | — |  | `10` |
| `filament_color` | string | — |  | `#4d9398` |
| `filament_cooling_before_tower` | integer | — |  | `10` |
| `filament_cooling_final_speed` | float[] | Speed of the last cooling move | Cooling moves are gradually accelerating towards this speed. | `0`, `3.4`, `3.5` |
| `filament_cooling_initial_speed` | float[] | Speed of the first cooling move | Cooling moves are gradually accelerating beginning at this speed. | `0`, `10`, `2.2` |
| `filament_cooling_moves` | integer[] | Number of cooling moves | Filament is cooled by being moved back and forth in the | `0`, `2`, `4` |
| `filament_cost` | float[] | Price | Filament price. For statistics only. | `0`, `20`, `30` |
| `filament_density` | float[] | Density | Filament density. For statistics only. | `0`, `1.04`, `1.24` |
| `filament_deretraction_speed` | string | — |  | `0`, `25`, `80` |
| `filament_diameter` | float[] | Diameter | Filament diameter is used to calculate extrusion in G-code, so it is important and should be accurate. | `1.12838`, `1.75`, `2.8` |
| `filament_enable_overhang_speed` | boolean | — |  | `1` |
| `filament_end_gcode` | string[] | End G-code | End G-code when finishing the printing of this filament. |  |
| `filament_extruder_id` | integer | Filament extruder ID | The current extruder ID. The same as current_extruder. | `1` |
| `filament_extruder_variant` | string[] | — |  | `Direct Drive High Flow`, `Direct Drive Standard` |
| `filament_flow_ratio` | float[] | Flow ratio | The material may have volumetric change after switching between molten and crystalline states. | `0.95`, `0.98`, `1` |
| `filament_flow_ratio_initial_layer` | float | — |  | `1.08` |
| `filament_flush_temp` | integer[] | Flush temperature | Temperature when flushing filament. 0 indicates the upper bound of the recommended nozzle temperature range. | `0`, `200`, `240` |
| `filament_flush_volumetric_speed` | float[] | Flush volumetric speed | Volumetric speed when flushing filament. 0 indicates the max volumetric speed. | `0`, `3` |
| `filament_id` | string | — |  | `GFLLK10PETG`, `GFLLK10PLA`, `GFLLK10PPETG` |
| `filament_is_support` | boolean[] | Support material | Support material is commonly used to print supports and support interfaces. | `0`, `1` |
| `filament_load_time` | float | — |  | `0`, `10.5`, `31.925` |
| `filament_loading_speed` | float[] | Loading speed | Speed used for loading the filament on the wipe tower. | `0`, `10`, `14` |
| `filament_loading_speed_start` | float[] | Loading speed at the start | Speed used at the very beginning of loading phase. | `0`, `19`, `3` |
| `filament_long_retractions_when_cut` | string | — |  | `0`, `1`, `nil` |
| `filament_long_retractions_when_ec` | string | — |  | `nil` |
| `filament_max_volumetric_speed` | float[] | Max volumetric speed | This setting stands for how much volume of filament can be melted and extruded per second. | `0`, `10`, `12` |
| `filament_minimal_purge_on_wipe_tower` | float[] | Minimal purge on wipe tower | After a tool change, the exact position of the newly loaded filament inside | `15`, `35`, `40` |
| `filament_multitool_ramming` | boolean[] | Enable ramming for multi-tool setups | Perform ramming when using multi-tool printer (i.e. when the 'Single Extruder Multimaterial' in Printer Settings is unchecked). | `0`, `1` |
| `filament_multitool_ramming_flow` | float[] | Multi-tool ramming flow | Flow used for ramming the filament before the tool change. | `0`, `10`, `2.5` |
| `filament_multitool_ramming_volume` | float[] | Multi-tool ramming volume | The volume to be rammed before the tool change. | `0`, `10`, `40` |
| `filament_notes` | string[] | Filament notes | You can put your notes regarding the filament here. | ``, `Flexible filament - print slowly with minimal retraction`, `High clarity and chemical resistance copolyester` |
| `filament_overhang_1_4_speed` | boolean | — |  | `0` |
| `filament_overhang_2_4_speed` | integer | — |  | `50` |
| `filament_overhang_3_4_speed` | integer | — |  | `30` |
| `filament_overhang_4_4_speed` | integer | — |  | `10` |
| `filament_overhang_totally_speed` | integer | — |  | `10` |
| `filament_pre_cooling_temperature` | integer | — |  | `0`, `195`, `200` |
| `filament_pre_cooling_temperature_nc` | boolean | — |  | `0` |
| `filament_prime_volume` | integer | — |  | `30`, `45` |
| `filament_prime_volume_nc` | integer | — |  | `60` |
| `filament_printable` | integer[] | Filament printable | The filament is printable in extruder. | `1`, `2`, `3` |
| `filament_ramming_parameters` | string[] | Ramming parameters | This string is edited by RammingDialog and contains ramming specific parameters. |  |
| `filament_ramming_travel_time` | integer | — |  | `0`, `20` |
| `filament_ramming_travel_time_nc` | boolean | — |  | `0` |
| `filament_ramming_volumetric_speed` | float | — |  | `-1`, `0.55`, `0.7` |
| `filament_ramming_volumetric_speed_nc` | integer | — |  | `-1` |
| `filament_retract_before_wipe` | string | — |  | `0%`, `70%`, `80%` |
| `filament_retract_layer_change` | boolean | — |  | `0`, `1` |
| `filament_retract_lift_above` | string | — |  | `0`, `0.3`, `nil` |
| `filament_retract_lift_below` | string | — |  | `0.6`, `1.5`, `249` |
| `filament_retract_lift_enforce` | string | — |  | `All Surfaces`, `nil` |
| `filament_retract_restart_extra` | string | — |  | `0`, `0.0`, `nil` |
| `filament_retract_when_changing_layer` | string | — |  | `0`, `1`, `nil` |
| `filament_retraction_distances_when_cut` | string | — |  | `0`, `10`, `18` |
| `filament_retraction_distances_when_ec` | string | — |  | `nil` |
| `filament_retraction_length` | string | — |  | `0.4`, `1.2`, `2` |
| `filament_retraction_minimum_travel` | string | — |  | `1`, `2`, `3` |
| `filament_retraction_speed` | string | — |  | `30`, `40`, `80` |
| `filament_scarf_gap` | percent | — |  | `0%`, `15%` |
| `filament_scarf_height` | percent | — |  | `10%`, `5%` |
| `filament_scarf_length` | integer | — |  | `10` |
| `filament_scarf_seam_type` | string | — |  | `all`, `none` |
| `filament_settings_id` | string[] | — |  | ``, `Chuanying Generic HIPS`, `Chuanying PETG @Chuanying X1 0.25 Nozzle` |
| `filament_shrink` | percent[] | Shrinkage (XY) | Enter the shrinkage percentage that the filament will get after cooling (94% if you measure 94mm instead of 100mm). | `100%`, `99.5%`, `99.7%` |
| `filament_shrinkage_compensation_z` | percent[] | Shrinkage (Z) | Enter the shrinkage percentage that the filament will get after cooling (94% if you measure 94mm instead of 100mm). | `100%`, `99%`, `99.18%` |
| `filament_soluble` | boolean[] | Soluble material | Soluble material is commonly used to print supports and support interfaces. | `0`, `1` |
| `filament_spool_weight` | integer | — |  | `1000`, `900` |
| `filament_stamping_distance` | float[] | Stamping distance measured from the center of the cooling tube | If set to non-zero value, filament is moved toward the nozzle between the individual cooling moves (\ | `0`, `45` |
| `filament_stamping_loading_speed` | float[] | Stamping loading speed | Speed used for stamping. | `0`, `29` |
| `filament_start_gcode` | string[] | Start G-code | Start G-code when starting the printing of this filament. |  |
| `filament_toolchange_delay` | float[] | Delay after unloading | Time to wait after the filament is unloaded. | `0` |
| `filament_tower_interface_pre_extrusion_dist` | float[] | Interface layer pre-extrusion distance | Pre-extrusion distance for prime tower interface layer (where different materials meet). | `10` |
| `filament_tower_interface_pre_extrusion_length` | float[] | Interface layer pre-extrusion length | Pre-extrusion length for prime tower interface layer (where different materials meet). | `0` |
| `filament_tower_interface_print_temp` | integer[] | Interface layer print temperature | Print temperature for prime tower interface layer (where different materials meet). If set to -1, use max recommended nozzle temperature. | `-1` |
| `filament_tower_interface_purge_volume` | float[] | Interface layer purge length | Purge length for prime tower interface layer (where different materials meet). | `20` |
| `filament_tower_ironing_area` | float[] | Tower ironing area | Ironing area for prime tower interface layer (where different materials meet). | `4` |
| `filament_type` | string[] | Type | The material type of filament. | `HIPS`, `PETG`, `PLA` |
| `filament_unload_time` | float | — |  | `0`, `12`, `24.75` |
| `filament_unloading_speed` | float[] | Unloading speed | Speed used for unloading the filament on the wipe tower (does not affect | `0`, `100`, `35` |
| `filament_unloading_speed_start` | float[] | Unloading speed at the start | Speed used for unloading the tip of the filament immediately after ramming. | `0`, `100`, `35` |
| `filament_velocity_adaptation_factor` | boolean | — |  | `1` |
| `filament_vendor` | string[] | Vendor | Vendor of filament. For show only. | `Anycubic`, `Artillery`, `Generic` |
| `filament_wipe` | string | — |  | `0`, `1`, `nil` |
| `filament_wipe_distance` | string | — |  | `1`, `2`, `4` |
| `filament_z_hop` | string | — |  | `0`, `0.4`, `1.5` |
| `filament_z_hop_types` | string | — |  | `Auto Lift`, `Slope Lift`, `Spiral Lift` |
| `first_layer_temperature` | integer[] | First layer temperature | Vector of first layer temperatures for each extruder/filament. | `220` |
| `first_x_layer_fan_speed` | integer | — |  | `0`, `40` |
| `full_fan_speed_layer` | integer[] | Full fan speed at layer | Fan speed will be ramped up linearly from zero at layer \ | `0`, `100`, `3` |
| `hole_coef_1` | boolean | — |  | `0` |
| `hole_coef_2` | float | — |  | `-0.006`, `-0.0081`, `-0.0103` |
| `hole_coef_3` | float | — |  | `0.18`, `0.2`, `0.2041` |
| `hole_limit_max` | float | — |  | `0.14`, `0.22` |
| `hole_limit_min` | float | — |  | `-0.088`, `0.068`, `0.08` |
| `hot_plate_temp` | integer[] | Bed temperature | Bed temperature for layers except the initial one. | `45`, `60`, `70` |
| `hot_plate_temp_initial_layer` | integer[] | Initial layer bed temperature | Bed temperature of the initial layer. | `45`, `60`, `70` |
| `idle_temperature` | integer[] | Idle temperature | Nozzle temperature when the tool is currently not used in multi-tool setups. | `0`, `130`, `150` |
| `idle_temperture` | boolean | — |  | `0` |
| `impact_strength_z` | float | — |  | `10.6`, `13.8`, `6.6` |
| `internal_bridge_fan_speed` | integer[] | Internal bridges fan speed | The part cooling fan speed used for all internal bridges. Set to -1 to use the overhang fan speed settings instead. | `-1` |
| `ironing_fan_speed` | integer[] | Ironing fan speed | This part cooling fan speed is applied when ironing. Setting this parameter to a lower than regular speed | `-1` |
| `is_custom_defined` | boolean | — |  | `0` |
| `long_retractions_when_ec` | boolean[] | Long retraction when extruder change |  | `0`, `1` |
| `mixing_stepper_rotation_volume` | integer | — |  | `8000` |
| `multi_zone_1_initial_layer` | integer | — |  | `200`, `240` |
| `multi_zone_1_temperature` | integer | — |  | `200`, `240` |
| `multi_zone_2_initial_layer` | integer | — |  | `200`, `240` |
| `multi_zone_2_temperature` | integer | — |  | `200`, `240` |
| `multi_zone_3_initial_layer` | integer | — |  | `200`, `220` |
| `multi_zone_3_temperature` | integer | — |  | `200`, `220` |
| `no_slow_down_for_cooling_on_outwalls` | boolean | — |  | `0` |
| `nozzle_temperature` | integer[] | Nozzle temperature | Nozzle temperature for layers after the initial one. | `200`, `205`, `250` |
| `nozzle_temperature_initial_layer` | integer[] | Initial layer nozzle temperature | Nozzle temperature for printing initial layer when using this filament. | `200`, `210`, `240` |
| `nozzle_temperature_intial_layer` | integer | — |  | `205`, `215`, `255` |
| `nozzle_temperature_range_high` | integer[] | Max |  | `210`, `230`, `250` |
| `nozzle_temperature_range_low` | integer[] | Min |  | `190`, `200`, `205` |
| `overhang_fan_speed` | integer[] | Overhangs and external bridges fan speed | Use this part cooling fan speed when printing bridges or overhang walls with an overhang threshold that exceeds | `100`, `50`, `80` |
| `overhang_fan_threshold` | enum[] | Overhang cooling activation threshold | When the overhang exceeds this specified threshold, force the cooling fan to run at the 'Overhang Fan Speed' set below. | `10%`, `25%`, `50%` |
| `overhang_threshold_participating_cooling` | percent | — |  | `100%` |
| `override_process_overhang_speed` | boolean | — |  | `0` |
| `pellet_flow_coefficient` | float[] | Pellet flow coefficient | Pellet flow coefficient is empirically derived and allows for volume calculation for pellet printers.  Internally it is converted to filament_diameter. All… | `0.4157`, `1` |
| `pre_start_fan_time` | integer | — |  | `0`, `2` |
| `pressure_advance` | float[] | Pressure advance | Pressure advance (Klipper) AKA Linear advance factor (Marlin). | `0.035`, `0.036`, `0.046` |
| `reduce_fan_stop_start_freq` | boolean[] | Keep fan always on | Enabling this setting means that the part cooling fan will never stop completely | `0`, `1` |
| `renamed_from` | string | — |  | `My Generic ABS @MyToolChanger`, `My Generic ASA @MyToolChanger`, `My Generic PC @MyToolChanger` |
| `required_nozzle_HRC` | integer[] | Required nozzle HRC | Minimum HRC of nozzle required to print the filament. Zero means no checking of nozzle's HRC. | `0`, `3`, `40` |
| `retraction_distances_when_ec` | float[] | Retraction distance when extruder change |  | `0`, `10`, `4` |
| `settings_id` | string | — |  | `IQS3` |
| `slow_down_for_layer_cooling` | boolean[] | Slow printing down for better layer cooling | Enable this option to slow printing speed down to make the final layer time not shorter than | `0`, `1` |
| `slow_down_layer_time` | float[] | Layer time | The printing speed in exported G-code will be slowed down when the estimated layer time is | `4`, `5`, `6` |
| `slow_down_min_speed` | float[] | Min print speed | The minimum print speed to which the printer slows down to maintain the minimum layer time defined above | `10`, `15`, `20` |
| `supertack_plate_temp` | integer[] | Bed temperature | Bed temperature for layers except the initial one. | `0`, `45`, `70` |
| `supertack_plate_temp_initial_layer` | integer[] | Initial layer bed temperature | Bed temperature of the initial layer. | `0`, `45`, `70` |
| `support_material_interface_fan_speed` | integer[] | Support interface fan speed | This part cooling fan speed is applied when printing support interfaces. Setting this parameter to a higher than regular speed | `100`, `40`, `80` |
| `temp_max` | integer | — |  | `230`, `240`, `260` |
| `temp_min` | integer | — |  | `190`, `210`, `230` |
| `temperature_vitrification` | integer[] | Softening temperature | The material softens at this temperature, so when the bed temperature is equal to or greater than this, | `100`, `108`, `60` |
| `temperture_vitrification` | integer | — |  | `148`, `156`, `55` |
| `textured_cool_plate_temp` | integer[] | Bed temperature | Bed temperature for layers except the initial one. | `0`, `40`, `45` |
| `textured_cool_plate_temp_initial_layer` | integer[] | Initial layer bed temperature | Bed temperature of the initial layer. | `0`, `40`, `45` |
| `textured_plate_temp` | integer[] | Bed temperature | Bed temperature for layers except the initial one. | `100`, `110`, `60` |
| `textured_plate_temp_initial_layer` | integer[] | Initial layer bed temperature | Bed temperature of the initial layer. | `100`, `55`, `60` |
| `version` | string | — |  | `1.0.0.0`, `2.0.2.0`, `2.1.1.0` |
| `volumetric_speed_coefficients` | string[] | Max volumetric speed multinomial coefficients |  | `-72.1736 -8.8086 10.6201 29.6592 9.9286 -3.3920`, `0 0 0 0 0 0`, `12.4717 -2.8601 9.7830 2.9503 3.9235 -1.8644` |


---

## 3. Process Profile Fields

Process profiles contain all **slicing parameters**: layer height, perimeter/wall counts,
infill pattern and density, print speeds, support generation, bridging, etc.

> For array types (e.g. `integer[]`, `float[]`), the **Examples** column shows sample *element* values
> seen across profiles, not a full array literal.

### Process Profile Fields

| Field | Data Type | Label | Description | Examples / Enum Values |
|-------|-----------|-------|-------------|------------------------|
| `accel_to_decel` | percent | — |  | `50%` |
| `accel_to_decel_enable` | boolean | Enable accel_to_decel | Klipper's max_accel_to_decel will be adjusted automatically. | `0`, `1` |
| `accel_to_decel_factor` | percent | accel_to_decel | Klipper's max_accel_to_decel will be adjusted to this %% of acceleration. | `100`, `25%`, `30%` |
| `acceleration_limit_mess_enable` | boolean | — |  | `0` |
| `adaptive_layer_height` | boolean | Adaptive layer height | Enabling this option means the height of every layer except the first will be automatically calculated | `0`, `1` |
| `ai_infill` | boolean | — |  | `0` |
| `align_infill_direction_to_model` | boolean | Align infill direction to model | Aligns infill and surface fill directions to follow the model's orientation on the build plate. When enabled, fill directions rotate with the model to maintain… | `1` |
| `alternate_extra_wall` | boolean | Alternate extra wall | This setting adds an extra wall to every other layer. This way the infill gets wedged vertically between the walls, resulting in stronger prints. | `0` |
| `apply_top_surface_compensation` | boolean | — |  | `0` |
| `avoid_crossing_wall_includes_support` | boolean | — |  | `0` |
| `bottom_color_penetration_layers` | integer | — |  | `3`, `4`, `5` |
| `bottom_shell_layers` | integer | Bottom shell layers | This is the number of solid layers of bottom shell, including the bottom | `3`, `4`, `6` |
| `bottom_shell_thickness` | float | Bottom shell thickness | The number of bottom solid layers is increased when slicing if the thickness calculated by bottom shell layers is | `0`, `0.5`, `0.6` |
| `bottom_solid_infill_flow_ratio` | float | Bottom surface flow ratio | This factor affects the amount of material for bottom solid infill. | `0.96`, `0.98`, `1` |
| `bottom_surface_pattern` | enum | Bottom surface pattern | Line pattern of bottom surface infill, not bridge infill. | `monotonic`, `monotonicline`, `rectilinear` |
| `bridge_acceleration` | float|percent | Bridge | Acceleration of bridges. If the value is expressed as a percentage (e.g. 50%), it will be calculated based on the outer wall acceleration. | `2000`, `3000`, `50%` |
| `bridge_angle` | float | External bridge infill direction | Bridging angle override. If left to zero, the bridging angle will be calculated | `0` |
| `bridge_density` | percent | External bridge density | Controls the density (spacing) of external bridge lines. Default is 100%. | `100%` |
| `bridge_flow` | float | Bridge flow ratio | Decrease this value slightly (for example 0.9) to reduce the amount of material for bridge, to improve sag. | `0.80`, `0.85`, `0.95` |
| `bridge_infill_acceleration` | percent | — |  | `50%` |
| `bridge_no_support` | boolean | Don't support bridges | Don't support the whole bridge area which make support very large. | `0`, `1` |
| `bridge_speed` | float | External | Speed of the externally visible bridge extrusions. | `25`, `30`, `40` |
| `brim_ears_detection_length` | float | Brim ear detection radius | The geometry will be decimated before detecting sharp angles. | `1` |
| `brim_ears_max_angle` | float | Brim ear max angle | Maximum angle to let a brim ear appear. | `125` |
| `brim_object_gap` | float | Brim-object gap | A gap between innermost brim line and object can make brim be removed more easily. | `0`, `0.1`, `0.12` |
| `brim_type` | enum | Brim type | This controls the generation of the brim at outer and/or inner side of models. | `auto_brim`, `no_brim`, `outer_only` |
| `brim_width` | float | Brim width | Distance from model to the outermost brim line. | `0`, `3`, `5` |
| `circle_compensation_manual_offset` | boolean | — |  | `0` |
| `compatible_printers` | string[] | Select printers |  | `LONGER LK10 (0.2 nozzle)`, `LONGER LK10 (0.4 nozzle)`, `LONGER LK10 Plus (0.4 nozzle)` |
| `compatible_printers_condition` | string | Condition | A boolean expression using the configuration values of an active printer profile. | ``, `printer_notes=~/.*MK4S.*/ and nozzle_diameter[0]==0.25`, `printer_notes=~/.*MK4S.*/ and nozzle_diameter[0]==0.4` |
| `counterbore_hole_bridging` | enum | Bridge counterbore holes |  | `none`, `partiallybridge` |
| `default_acceleration` | float | Normal printing | The default acceleration of both normal printing and travel except initial layer. | `1000`, `10000`, `500` |
| `default_jerk` | float | Default | Default jerk. | `0`, `10`, `20` |
| `description` | string | — |  | `Standard profile for 0.4mm nozzle, prioritizing speed.`, `Standard profile for 0.8mm nozzle, prioritizing speed.`, `Strength profile for 0.6mm nozzle, prioritizing strength.` |
| `detect_floating_vertical_shell` | boolean | — |  | `1` |
| `detect_narrow_internal_solid_infill` | boolean | Detect narrow internal solid infill | This option will auto-detect narrow internal solid infill areas. | `0`, `1` |
| `detect_overhang_wall` | boolean | Detect overhang wall | Detect the overhang percentage relative to line width and use different speed to print. | `0`, `1` |
| `detect_thin_wall` | boolean | Detect thin wall | Detect thin wall which can't contain two line width. And use single line to print. | `0`, `1` |
| `dont_filter_internal_bridges` | enum | Filter out small internal bridges | This option can help reduce pillowing on top surfaces in heavily slanted or curved models. | `disabled`, `limited`, `nofilter` |
| `draft_shield` | enum | Draft shield | A draft shield is useful to protect an ABS or ASA print from warping and detaching from print bed due to wind draft. | `disabled`, `enabled` |
| `elefant_foot_compensation` | float | Elephant foot compensation | Shrinks the initial layer on build plate to compensate for elephant foot effect. | `0`, `0.075`, `0.1` |
| `elefant_foot_compensation_layers` | integer | Elephant foot compensation layers | The number of layers on which the elephant foot compensation will be active. | `1`, `5` |
| `embedding_wall_into_infill` | boolean | — |  | `0` |
| `enable_arc_fitting` | boolean | Arc fitting | Enable this to get a G-code file which has G2 and G3 moves. | `0`, `1` |
| `enable_circle_compensation` | boolean | — |  | `0` |
| `enable_extra_bridge_layer` | enum | Extra bridge layers (beta) | This option enables the generation of an extra bridge layer over internal and/or external bridges. | `disabled`, `external_bridge_only`, `internal_bridge_only`, `apply_to_all` |
| `enable_height_slowdown` | boolean | — |  | `0` |
| `enable_overhang_speed` | boolean | Slow down for overhang | Enable this option to slow printing down for different overhang degree. | `0`, `1` |
| `enable_prime_tower` | boolean | Enable | The wiping tower can be used to clean up the residue on the nozzle and stabilize the chamber pressure inside the nozzle, | `0`, `1` |
| `enable_support` | boolean | Enable support | Enable support generation. | `0`, `1` |
| `enable_tower_interface_cooldown_during_tower` | boolean | Cool down from interface boost during prime tower | When interface-layer temperature boost is active, set the nozzle back to print temperature at the start of the prime tower so it cools down during the tower. | `0` |
| `enable_tower_interface_features` | boolean | Enable tower interface features | Enable optimized prime tower interface behavior when different materials meet. | `0` |
| `enable_wrapping_detection` | boolean | Enable clumping detection | Enable clumping detection | `0` |
| `end_gcode` | string | — |  |  |
| `enforce_support_layers` | integer | Enforce support for the first n layers | Generate support material for the specified number of layers counting from bottom, | `0` |
| `ensure_vertical_shell_thickness` | enum | Ensure vertical shell thickness |  | `none`, `ensure_critical_only`, `ensure_moderate`, `ensure_all` |
| `exclude_object` | boolean | Exclude objects | Enable this option to add EXCLUDE OBJECT command in G-code. | `0`, `1` |
| `extra_perimeters_on_overhangs` | boolean | Extra perimeters on overhangs | Create additional perimeter paths over steep overhangs and areas where bridges cannot be anchored. | `0`, `1` |
| `filename_format` | string | Filename format | Users can define the project file name when exporting. | `{input_filename_base}.gcode`, `{input_filename_base}_{filament_type[0]}.gcode`, `{input_filename_base}_{filament_type[0]}_{print_time}.gcode` |
| `fill_multiline` | integer | Fill Multiline | Using multiple lines for the infill pattern, if supported by infill pattern. | `1` |
| `filter_out_gap_fill` | float | Filter out tiny gaps | Don't print gap fill with a length is smaller than the threshold specified (in mm). This setting applies to top, | `0`, `0.1`, `0.5` |
| `first_x_layer_fan_speed` | boolean | — |  | `0` |
| `flush_into_infill` | boolean | Flush into objects' infill | Purging after filament change will be done inside objects' infills. | `0` |
| `flush_into_objects` | boolean | Flush into this object | This object will be used to purge the nozzle after a filament change to save filament and decrease the print time. | `0` |
| `flush_into_support` | boolean | Flush into objects' support | Purging after filament change will be done inside objects' support. | `0`, `1` |
| `fuzzy_skin` | enum | Fuzzy Skin | Randomly jitter while printing the wall, so that the surface has a rough look. This setting controls | `none`, `external`, `all`, `allwalls`, `disabled_fuzzy` |
| `fuzzy_skin_first_layer` | boolean | Apply fuzzy skin to first layer | Whether to apply fuzzy skin on the first layer. | `0` |
| `fuzzy_skin_point_distance` | float | Fuzzy skin point distance | The average distance between the random points introduced on each line segment. | `0.8` |
| `fuzzy_skin_thickness` | float | Fuzzy skin thickness | The width within which to jitter. It's advised to be below outer wall line width. | `0.3` |
| `gap_fill_enabled` | boolean | — |  | `0` |
| `gap_fill_target` | enum | Apply gap fill | Enables gap fill for the selected solid surfaces. The minimum gap length that will be filled can be controlled | `everywhere`, `topbottom`, `nowhere` |
| `gap_infill_speed` | float | Gap infill | Speed of gap infill. Gap usually has irregular line width and should be printed more slowly. | `120`, `150`, `200` |
| `gcode_add_line_number` | boolean | Add line number | Enable this to add line number(Nx) at the beginning of each G-code line. | `0` |
| `gcode_comments` | boolean | Verbose G-code | Enable this to get a commented G-code file, with each line explained by a descriptive text. | `0`, `1` |
| `gcode_label_objects` | boolean | Label objects | Enable this to add comments into the G-code labeling print moves with what object they belong to, | `0`, `1` |
| `hole_to_polyhole` | boolean | Convert holes to polyholes | Search for almost-circular holes that span more than one layer and convert the geometry to polyholes. | `0` |
| `hole_to_polyhole_threshold` | float|percent | Polyhole detection margin | Maximum defection of a point to the estimated radius of the circle. | `0.01` |
| `hole_to_polyhole_twisted` | boolean | Polyhole twist | Rotate the polyhole every layer. | `1` |
| `independent_support_layer_height` | boolean | Independent support layer height | Support layer uses layer height independent with object layer. This is to support customizing Z-gap and save print time. | `0`, `1` |
| `ineternal_bridge_speed` | integer | — |  | `70` |
| `infill_anchor` | float|percent | Sparse infill anchor length | Connect an infill line to an internal perimeter with a short segment of an additional perimeter. | `0`, `1`, `2`, `5`, `10`, `1000` |
| `infill_anchor_max` | float|percent | Maximum length of the infill anchor | Connect an infill line to an internal perimeter with a short segment of an additional perimeter. | `10`, `12`, `15` |
| `infill_combination` | boolean | Infill combination | Automatically Combine sparse infill of several layers to print together to reduce time. Wall is still printed | `0`, `1` |
| `infill_combination_max_layer_height` | float|percent | Infill combination - Max layer height | Maximum layer height for the combined sparse infill. | `100%`, `75%` |
| `infill_direction` | float | Sparse infill direction | Angle for sparse infill pattern, which controls the start or main direction of line. | `45`, `90` |
| `infill_instead_top_bottom_surfaces` | boolean | — |  | `0` |
| `infill_jerk` | float | Infill | Jerk for infill. | `3`, `5`, `8` |
| `infill_layer_acceleration` | integer | — |  | `1500` |
| `infill_lock_depth` | float | Infill lock depth | The parameter sets the overlapping depth between the interior and skin. | `1.0` |
| `infill_rotate_step` | boolean | — |  | `0` |
| `infill_shift_step` | float | Infill shift step | This parameter adds a slight displacement to each layer of infill to create a cross texture. | `0.4` |
| `infill_wall_overlap` | percent | Infill/Wall overlap | Infill area is enlarged slightly to overlap with wall for better bonding. | `15%`, `23%`, `25%` |
| `inital_layer_height` | float | — |  | `0.2`, `0.3`, `0.35` |
| `inital_travel_speed` | integer | — |  | `25` |
| `initial_layer_acceleration` | float | Initial layer | Acceleration of initial layer. Using a lower value can improve build plate adhesion. | `0`, `1000`, `2000` |
| `initial_layer_infill_speed` | float | Initial layer infill | Speed of solid infill part of initial layer. | `55`, `65`, `70` |
| `initial_layer_jerk` | float | Initial layer | Jerk for initial layer. | `5`, `7`, `8` |
| `initial_layer_line_width` | float|percent | Initial layer | Line width of initial layer. If expressed as a %, it will be computed over the nozzle diameter. | `0.22`, `0.42`, `0.62` |
| `initial_layer_min_bead_width` | percent | First layer minimum wall width | The minimum wall width that should be used for the first layer is recommended to be set | `100`, `85%` |
| `initial_layer_print_height` | float | Initial layer height | Height of initial layer. Making initial layer height to be thick slightly can improve build plate adhesion. | `0.15`, `0.2`, `0.24` |
| `initial_layer_speed` | float | Initial layer | Speed of initial layer except the solid infill part. | `20`, `35`, `45` |
| `initial_layer_travel_acceleration` | integer | — |  | `6000` |
| `initial_layer_travel_speed` | float|percent | Initial layer travel speed | Travel speed of initial layer. | `100`, `100%`, `50%` |
| `initial_solid_infill_acceleration` | integer | — |  | `3000` |
| `initial_travel_speed` | percent | — |  | `100%` |
| `inner_wall_acceleration` | float | Inner wall | Acceleration of inner walls. | `1000`, `3000`, `500` |
| `inner_wall_jerk` | float | Inner wall | Jerk of inner walls. | `10`, `15`, `7` |
| `inner_wall_line_width` | float|percent | Inner wall | Line width of inner wall. If expressed as a %, it will be computed over the nozzle diameter. | `0.25`, `0.45`, `0.65` |
| `inner_wall_speed` | float | Inner wall | Speed of inner wall. | `150`, `200`, `300` |
| `interface_shells` | boolean | Interface shells | Force the generation of solid shells between adjacent materials/volumes. | `0` |
| `interlocking_beam` | boolean | Use beam interlocking | Generate interlocking beam structure at the locations where different filaments touch. This improves the adhesion between filaments, especially models printed… | `0`, `1` |
| `interlocking_beam_layer_count` | integer | Interlocking beam layers | The height of the beams of the interlocking structure, measured in number of layers. Less layers is stronger, but more prone to defects. | `2` |
| `interlocking_beam_width` | float | Interlocking beam width | The width of the interlocking structure beams. | `0.8`, `1` |
| `interlocking_boundary_avoidance` | integer | Interlocking boundary avoidance | The distance from the outside of a model where interlocking structures will not be generated, measured in cells. | `2` |
| `interlocking_depth` | integer | Interlocking depth | The distance from the boundary between filaments to generate interlocking structure, measured in cells. Too few cells will result in poor adhesion. | `2` |
| `interlocking_orientation` | float | Interlocking direction | Orientation of interlock beams. | `22.5` |
| `internal_bridge_angle` | float | Internal bridge infill direction | Internal bridging angle override. If left to zero, the bridging angle will be calculated | `0` |
| `internal_bridge_density` | percent | Internal bridge density | Controls the density (spacing) of internal bridge lines. 100% means solid bridge. Default is 100%. | `100%` |
| `internal_bridge_flow` | float | Internal bridge flow ratio | This value governs the thickness of the internal bridge layer. This is the first layer over sparse infill. Decrease this value slightly (for example 0.9) to… | `0.95`, `0.96`, `0.98` |
| `internal_bridge_speed` | float|percent | Internal | Speed of internal bridges. If the value is expressed as a percentage, it will be calculated based on the bridge_speed. Default value is 150%. | `150%`, `50`, `70` |
| `internal_bridge_support_thickness` | float | — |  | `0`, `0.8` |
| `internal_solid_infill_acceleration` | float|percent | Internal solid infill | Acceleration of internal solid infill. If the value is expressed as a percentage (e.g. 100%), it will be calculated based on the default acceleration. | `100%`, `10000`, `5000` |
| `internal_solid_infill_line_width` | float|percent | Internal solid infill | Line width of internal solid infill. If expressed as a %, it will be computed over the nozzle diameter. | `0.25`, `0.45`, `0.65` |
| `internal_solid_infill_pattern` | enum | Internal solid infill pattern | Line pattern of internal solid infill. if the detect narrow internal solid infill be enabled, the concentric pattern will be used for the small area. | `alignedrectilinear`, `monotonic`, `monotonicline` |
| `internal_solid_infill_speed` | float | Internal solid infill | Speed of internal solid infill, not the top and bottom surface. | `150`, `200`, `250` |
| `ironing_angle` | float | Ironing angle offset | The angle of ironing lines offset from the top surface. | `-1`, `0` |
| `ironing_flow` | percent | Ironing flow | The amount of material to extrude during ironing. Relative to flow of normal layer height. | `10%`, `15%`, `30%` |
| `ironing_inset` | float | Ironing inset | The distance to keep from the edges. A value of 0 sets this to half of the nozzle diameter. | `0.11`, `0.21`, `0.31` |
| `ironing_pattern` | enum | Ironing Pattern | The pattern that will be used when ironing. | `rectilinear`, `concentric` |
| `ironing_spacing` | float | Ironing line spacing | The distance between the lines of ironing. | `0.08`, `0.1`, `0.15` |
| `ironing_speed` | float | Ironing speed | Print speed of ironing lines. | `15`, `20`, `30` |
| `ironing_type` | enum | Ironing Type | Ironing is using small flow to print on same height of surface again to make flat surface more smooth. | `no ironing`, `top`, `topmost`, `solid` |
| `is_custom_defined` | boolean | — |  | `0` |
| `is_infill_first` | boolean | Print infill first | Order of wall/infill. When the tickbox is unchecked the walls are printed first, which works best in most cases.  Printing infill first may help with extreme… | `0` |
| `layer_height` | float | Layer height | Slicing height for each layer. Smaller layer height means more accurate and more printing time. | `0.08`, `0.2`, `0.24` |
| `layer_heigth` | float | — |  | `0.24` |
| `layer_time_smoothing` | boolean | — |  | `0` |
| `layer_time_smoothing_threshold` | integer | — |  | `30` |
| `line_width` | float|percent | Default | Default line width if other line widths are set to 0. If expressed as a %, it will be computed over the nozzle diameter. | `0.2`, `0.4`, `0.6` |
| `locked_skeleton_infill_pattern` | string | — |  | `zigzag` |
| `locked_skin_infill_pattern` | string | — |  | `crosszag` |
| `machine_prepare_compensation_time` | integer | — |  | `260` |
| `make_overhang_printable` | boolean | Make overhangs printable | Modify the geometry to print overhangs without support material. | `0` |
| `make_overhang_printable_angle` | float | Make overhangs printable - Maximum angle | Maximum angle of overhangs to allow after making more steep overhangs printable. | `55`, `90` |
| `make_overhang_printable_hole_size` | float | Make overhangs printable - Hole area | Maximum area of a hole in the base of the model before it's filled by conical material. | `0` |
| `material_flow_dependent_temperature` | boolean | — |  | `0` |
| `material_flow_temp_graph` | string | — |  | `[[3.0,210],[10.0,220],[12.0,230]]` |
| `max_bridge_length` | float | Max bridge length | Max length of bridges that don't need support. Set it to 0 if you want all bridges to be supported, and set it to a very large value if you don't want any… | `0`, `10`, `20` |
| `max_travel_detour_distance` | float|percent | Avoid crossing walls - Max detour length | Maximum detour distance for avoiding crossing wall. | `0`, `300`, `50` |
| `max_volumetric_extrusion_rate_slope` | float | Extrusion rate smoothing | This parameter smooths out sudden extrusion rate changes that happen when | `0`, `100`, `20` |
| `max_volumetric_extrusion_rate_slope_segment_length` | float | Smoothing segment length | A lower value results in smoother extrusion rate transitions. | `3`, `5` |
| `min_bead_width` | percent | Minimum wall width | Width of the wall that will replace thin features (according to the Minimum feature size) | `100`, `80%`, `85%` |
| `min_feature_size` | percent | Minimum feature size | Minimum thickness of thin features. Model features that are thinner than this value will not be printed, | `20%`, `25%` |
| `min_length_factor` | float | Minimum wall length | Adjust this value to prevent short, unclosed walls from being printed, which could increase print time. | `0`, `0.5` |
| `min_skirt_length` | float | Skirt minimum extrusion length | Minimum filament extrusion length in mm when printing the skirt. Zero means this feature is disabled. | `0`, `30`, `4` |
| `min_width_top_surface` | float|percent | One wall threshold | If a top surface has to be printed and it's partially covered by another layer, it won't be considered at a top layer where its width is below this value. | `100%`, `300%`, `50%` |
| `minimum_sparse_infill_area` | float | Minimum sparse infill threshold | Sparse infill area which is smaller than threshold value is replaced by internal solid infill. | `0`, `10`, `15` |
| `minimum_sparse_infill_threshold` | boolean | — |  | `0` |
| `mmu_segmented_region_interlocking_depth` | float | Interlocking depth of a segmented region | Interlocking depth of a segmented region. It will be ignored if | `0` |
| `mmu_segmented_region_max_width` | float | Maximum width of a segmented region | Maximum width of a segmented region. Zero disables this feature. | `0` |
| `notes` | string | Configuration notes | You can put here your personal notes. This text will be added to the G-code | ``, `Process file version 1.0 20250620` |
| `only_one_wall_first_layer` | boolean | Only one wall on first layer | Use only one wall on first layer, to give more space to the bottom infill pattern. | `0`, `1` |
| `only_one_wall_top` | boolean | Only one wall on top surfaces | Use only one wall on flat top surfaces, to give more space to the top infill pattern. | `0`, `1` |
| `ooze_prevention` | boolean | Enable | This option will drop the temperature of the inactive extruders to prevent oozing. | `0`, `1` |
| `outer_wall_acceleration` | float | Outer wall | Acceleration of outer wall. Using a lower value can improve quality. | `1000`, `3000`, `5000` |
| `outer_wall_jerk` | float | Outer wall | Jerk of outer walls. | `10`, `15`, `7` |
| `outer_wall_line_width` | float|percent | Outer wall | Line width of outer wall. If expressed as a %, it will be computed over the nozzle diameter. | `0.2`, `0.4`, `0.6` |
| `outer_wall_speed` | float | Outer wall | Speed of outer wall which is outermost and visible. | `120`, `200`, `45` |
| `overhang_1_4_speed` | float|percent | — | Speed for line of wall which has degree of overhang between 10% and 25% line width. | `0`, `50`, `60` |
| `overhang_2_4_speed` | float|percent | — | Speed for line of wall which has degree of overhang between 25% and 50% line width. | `20`, `40`, `50` |
| `overhang_3_4_speed` | float|percent | — | Speed for line of wall which has degree of overhang between 50% and 75% line width. | `15`, `20`, `25` |
| `overhang_4_4_speed` | float|percent | — | Speed for line of wall which has degree of overhang between 75% and 100% line width. | `10`, `20`, `30` |
| `overhang_reverse` | boolean | Overhang reversal | Extrude perimeters that have a part over an overhang in the reverse direction on even layers. This alternating pattern can drastically improve steep overhangs… | `0`, `1` |
| `overhang_reverse_internal_only` | boolean | Reverse only internal perimeters | Apply the reverse perimeters logic only on internal perimeters. | `0`, `1` |
| `overhang_reverse_threshold` | float|percent | Overhang reversal threshold | Number of mm the overhang need to be for the reversal to be considered useful. Can be a % of the perimeter width. | `0%`, `50%` |
| `overhang_speed_classic` | boolean | — |  | `0`, `1` |
| `overhang_totally_speed` | integer | — |  | `10`, `19`, `24` |
| `override_filament_scarf_seam_setting` | boolean | — |  | `0` |
| `percise_outer_wall` | boolean | — |  | `1` |
| `percise_z_height` | float | — |  | `0.2` |
| `post_process` | string[] | Post-processing Scripts | If you want to process the output G-code through custom scripts, | `` |
| `pre_start_fan_time` | boolean | — |  | `0` |
| `precise_outer_wall` | boolean | Precise wall | Improve shell precision by adjusting outer wall spacing. This also improves layer consistency. NOTE: This option | `0`, `1` |
| `precise_z_height` | boolean | Precise Z height | Enable this to get precise Z height of object after slicing. | `0`, `1` |
| `preheat_steps` | integer | Preheat steps | Insert multiple preheat commands (e.g. M104.1). Only useful for Prusa XL. For other printers, please set it to 1. | `1`, `10` |
| `preheat_time` | float | Preheat time | To reduce the waiting time after tool change, Orca can preheat the next tool while the current tool is still in use. | `120`, `30`, `35` |
| `prime_tower_brim_width` | float | Brim width | Brim width of prime tower, negative number means auto calculated width based on the height of prime tower. | `-1` |
| `prime_tower_enable_framework` | boolean | Internal ribs | Enable internal ribs to increase the stability of the prime tower. | `0` |
| `prime_tower_enhance_type` | string | — |  | `chamfer` |
| `prime_tower_flat_ironing` | boolean | — |  | `0`, `1` |
| `prime_tower_lift_height` | integer | — |  | `-1` |
| `prime_tower_lift_speed` | integer | — |  | `90` |
| `prime_tower_max_speed` | integer | — |  | `90` |
| `prime_tower_width` | float | Width | Width of the prime tower. | `30`, `35`, `40` |
| `prime_volume` | float | Prime volume | The volume of material to prime extruder on tower. | `20`, `30`, `45` |
| `print_extruder_id` | integer[] | — |  | `1` |
| `print_extruder_variant` | string[] | — |  | `Direct Drive High Flow`, `Direct Drive Standard` |
| `print_flow_ratio` | float | Flow ratio | The material may have volumetric change after switching between molten and crystalline states. | `0.95`, `1`, `1.0` |
| `print_order` | enum | Intra-layer order | Print order within a single layer. | `default`, `as_obj_list` |
| `print_sequence` | enum | Print sequence | Print sequence, layer by layer or object by object. | `by layer`, `by object` |
| `print_settings_id` | string | — |  | ``, `0.12mm Standard @Chuanying X1 0.25 Nozzle`, `0.28mm Standard @Anycubic Kobra 2 Neo 0.4 nozzle` |
| `raft_contact_distance` | float | Raft contact Z distance | Z gap between object and raft. Ignored for soluble interface. | `0.1`, `0.12`, `0.2` |
| `raft_expansion` | float | Raft expansion | Expand all raft layers in XY plane. | `1.5` |
| `raft_first_layer_density` | percent | Initial layer density | Density of the first raft or support layer. | `100%`, `80%`, `90%` |
| `raft_first_layer_expansion` | float | Initial layer expansion | Expand the first raft or support layer to improve bed plate adhesion. | `2`, `3`, `3.5` |
| `raft_layers` | integer | Raft layers | Object will be raised by this number of support layers. | `0` |
| `reduce_crossing_wall` | boolean | Avoid crossing walls | Detour to avoid traveling across walls, which may cause blobs on the surface. | `0`, `1` |
| `reduce_infill_retraction` | boolean | Reduce infill retraction | Don't retract when the travel is entirely within an infill area. That means the oozing can't been seen. | `0`, `1` |
| `renamed_from` | string | — |  | `0.20mm Fast @Creality K1 SE 0.4`, `0.32mm Standard @MyToolChanger` |
| `resolution` | float | Resolution | The G-code path is generated after simplifying the contour of models to avoid too many points and G-code lines. | `0`, `0.008`, `0.012` |
| `role_based_wipe_speed` | boolean | Role base wipe speed | The wipe speed is determined by the speed of the current extrusion role. | `0`, `1` |
| `rotate_solid_infill_direction` | boolean | — |  | `1` |
| `scarf_angle_threshold` | integer | Conditional angle threshold |  | `155` |
| `scarf_joint_flow_ratio` | float | Scarf joint flow ratio | This factor affects the amount of material for scarf joints. | `0.95`, `1` |
| `scarf_joint_speed` | float|percent | Scarf joint speed |  | `100%`, `35`, `70` |
| `scarf_overhang_threshold` | percent | Conditional overhang threshold | This option determines the overhang threshold for the application of scarf joint seams. If the unsupported portion | `40%` |
| `seam_gap` | float|percent | Seam gap | In order to reduce the visibility of the seam in a closed loop extrusion, the loop is interrupted and shortened by a specified amount. | `0`, `10%`, `15%` |
| `seam_placement_away_from_overhangs` | boolean | — |  | `0` |
| `seam_position` | enum | Seam position | The start position to print each part of outer wall. | `nearest`, `aligned`, `aligned_back`, `back`, `random` |
| `seam_slope_conditional` | boolean | Conditional scarf joint | Apply scarf joints only to smooth perimeters where traditional seams do not conceal the seams at sharp corners effectively. | `0`, `1` |
| `seam_slope_entire_loop` | boolean | Scarf around entire wall | The scarf extends to the entire length of the wall. | `0`, `1` |
| `seam_slope_gap` | boolean | — |  | `0` |
| `seam_slope_inner_walls` | boolean | Scarf joint for inner walls | Use scarf joint for inner walls as well. | `0`, `1` |
| `seam_slope_min_length` | float | Scarf length | Length of the scarf. Setting this parameter to zero effectively disables the scarf. | `0`, `10`, `12` |
| `seam_slope_start_height` | float|percent | Scarf start height | Start height of the scarf. | `0`, `0.1`, `10%` |
| `seam_slope_steps` | integer | Scarf steps | Minimum number of segments of each scarf. | `10`, `6` |
| `seam_slope_type` | enum | Scarf joint seam (beta) | Use scarf joint to minimize seam visibility and increase seam strength. | `none`, `external`, `all` |
| `single_extruder_multi_material_priming` | boolean | Prime all printing extruders | If enabled, all printing extruders will be primed at the front edge of the print bed at the start of the print. | `0`, `1` |
| `single_loop_draft_shield` | boolean | Single loop after first layer | Limits the skirt/draft shield loops to one wall after the first layer. This is useful, on occasion, to conserve filament but may cause the draft shield/skirt… | `0` |
| `skeleton_infill_density` | percent | Skeleton infill density | The remaining part of the model contour after removing a certain depth from the surface is called the skeleton. | `15%`, `25%` |
| `skeleton_infill_line_width` | float|percent | Skeleton line width | Adjust the line width of the selected skeleton paths. | `1.2`, `3.0`, `5.0` |
| `skin_infill_density` | percent | Skin infill density | The portion of the model's outer surface within a certain depth range is called the skin. | `15%`, `25%` |
| `skin_infill_depth` | float | Skin infill depth | The parameter sets the depth of skin. | `2.0` |
| `skin_infill_line_width` | float|percent | Skin line width | Adjust the line width of the selected skin paths. | `1.2`, `3.0`, `5.0` |
| `skirt_distance` | float | Skirt distance | The distance from the skirt to the brim or the object. | `1`, `2`, `3` |
| `skirt_height` | integer | Skirt height | How many layers of skirt. Usually only one layer. | `1`, `2`, `3` |
| `skirt_loops` | integer | Skirt loops | Number of loops for the skirt. Zero means disabling skirt. | `0`, `1`, `2` |
| `skirt_speed` | float | Skirt speed | Speed of skirt, in mm/s. Zero means use default layer extrusion speed. | `130`, `150`, `50` |
| `skirt_start_angle` | float | Skirt start point | Angle from the object center to skirt start point. Zero is the most right position, counter clockwise is positive angle. | `-135`, `-45` |
| `skirt_type` | enum | Skirt type | Combined - single skirt for all objects, Per object - individual object skirt. | `combined`, `perobject` |
| `slice_closing_radius` | float | Slice gap closing radius | Cracks smaller than 2x gap closing radius are being filled during the triangle mesh slicing. | `0.049` |
| `slicing_mode` | enum | Slicing Mode | Use \ | `regular`, `even_odd`, `close_holes` |
| `slow_down_curled_perimeters` | boolean | — |  | `0` |
| `slow_down_layers` | integer | Number of slow layers | The first few layers are printed slower than normal. | `0`, `1`, `2` |
| `slow_layers_count` | integer | — |  | `5` |
| `slowdown_end_acc` | integer | — |  | `100000` |
| `slowdown_end_height` | integer | — |  | `400` |
| `slowdown_end_speed` | integer | — |  | `1000` |
| `slowdown_for_curled_perimeters` | boolean | Slow down for curled perimeters | Enable this option to slow down printing in areas where perimeters may have curled upwards. | `0`, `1` |
| `slowdown_start_acc` | integer | — |  | `100000` |
| `slowdown_start_height` | boolean | — |  | `0` |
| `slowdown_start_speed` | integer | — |  | `1000` |
| `small_area_infill_flow_compensation` | boolean | Small area flow compensation (beta) | Enable flow compensation for small infill areas. | `0` |
| `small_area_infill_flow_compensation_model` | string[] | Flow Compensation Model |  | `0,0` |
| `small_perimeter_speed` | float|percent | Small perimeters | This separate setting will affect the speed of perimeters having radius <= small_perimeter_threshold | `140`, `30%`, `50` |
| `small_perimeter_threshold` | float | Small perimeters threshold | This sets the threshold for small perimeter length. Default threshold is 0mm. | `0`, `1`, `4` |
| `smooth_coefficient` | integer | — |  | `150`, `4`, `80` |
| `smooth_speed_discontinuity_area` | boolean | — |  | `1` |
| `solid_infill_direction` | float | Solid infill direction | Angle for solid infill pattern, which controls the start or main direction of line. | `0`, `45` |
| `solid_infill_filament` | integer | Solid infill | Filament to print solid infill. | `1` |
| `solid_infill_rotate_template` | string | Solid infill rotation template | This parameter adds a rotation of solid infill direction to each layer according to the specified template. | `` |
| `sparse_infill_acceleration` | float|percent | Sparse infill | Acceleration of sparse infill. If the value is expressed as a percentage (e.g. 100%), it will be calculated based on the default acceleration. | `100%`, `10000`, `5000` |
| `sparse_infill_anchor` | string | — |  | `2,5` |
| `sparse_infill_density` | percent | Sparse infill density | Density of internal sparse infill, 100% turns all sparse infill into solid infill and internal solid infill pattern will be used. | `10%`, `15%`, `20%` |
| `sparse_infill_filament` | integer | Infill | Filament to print internal sparse infill. | `1` |
| `sparse_infill_line_width` | float|percent | Sparse infill | Line width of internal sparse infill. If expressed as a %, it will be computed over the nozzle diameter. | `0.25`, `0.45`, `0.65` |
| `sparse_infill_patter` | string | — |  | `grid` |
| `sparse_infill_pattern` | enum | Sparse infill pattern | Line pattern for internal sparse infill. | `rectilinear`, `alignedrectilinear`, `zigzag`, `crosszag`, `lockedzag`, `line`, … (26 total) |
| `sparse_infill_speed` | float | Sparse infill | Speed of internal sparse infill. | `100`, `200`, `270` |
| `speed_limit_to_height_enable` | boolean | — |  | `0` |
| `spiral_mode` | boolean | Spiral vase | Spiralize smooths out the Z moves of the outer contour. | `0`, `1` |
| `spiral_mode_max_xy_smoothing` | float|percent | Max XY Smoothing | Maximum distance to move points in XY to try to achieve a smooth spiral. | `200%` |
| `spiral_mode_smooth` | boolean | Smooth Spiral | Smooth Spiral smooths out X and Y moves as well, | `0`, `1` |
| `staggered_inner_seams` | boolean | Staggered inner seams | This option causes the inner seams to be shifted backwards based on their depth, forming a zigzag pattern. | `0`, `1` |
| `standby_temperature_delta` | integer | Temperature variation | Temperature difference to be applied when an extruder is not active. | `-25`, `-40`, `-5` |
| `start_gcode` | string | — |  |  |
| `support_angle` | float | Pattern angle | Use this setting to rotate the support pattern on the horizontal plane. | `0`, `45` |
| `support_base_pattern` | enum | Base pattern | Line pattern of support. | `default`, `rectilinear`, `rectilinear-grid`, `honeycomb`, `lightning`, `hollow` |
| `support_base_pattern_spacing` | float | Base pattern spacing | Spacing between support lines. | `0.2`, `0.3`, `2` |
| `support_bottom_interface_spacing` | float | Bottom interface spacing | Spacing of bottom interface lines. Zero means solid interface. | `0.22`, `0.25`, `0.4` |
| `support_bottom_z_distance` | float | Bottom Z distance | The Z gap between the bottom support interface and object. | `0.12`, `0.18`, `0.22` |
| `support_critical_regions_only` | boolean | Support critical regions only | Only create support for critical regions including sharp tail, cantilever, etc. | `0`, `1` |
| `support_expansion` | float | Normal Support expansion | Expand (+) or shrink (-) the horizontal span of normal support. | `0`, `0.5`, `1` |
| `support_filament` | integer | Support/raft base | Filament to print support base and raft. \ | `0`, `1`, `2` |
| `support_interface_bottom_layers` | integer | Bottom interface layers | Number of bottom interface layers. | `-1` |
| `support_interface_bottom_spacing` | float | — |  | `0.5` |
| `support_interface_filament` | integer | Support/raft interface | Filament to print support interface. \ | `0`, `1`, `2` |
| `support_interface_loop_pattern` | boolean | Interface use loop pattern | Cover the top contact layer of the supports with loops. Disabled by default. | `0`, `1` |
| `support_interface_not_for_body` | boolean | Avoid interface filament for base | Avoid using support interface filament to print support base if possible. | `1` |
| `support_interface_pattern` | enum | Interface pattern | Line pattern of support interface. | `auto`, `rectilinear`, `concentric`, `rectilinear_interlaced`, `grid` |
| `support_interface_spacing` | float | Top interface spacing | Spacing of interface lines. Zero means solid interface. | `0`, `0.2`, `0.4` |
| `support_interface_speed` | float | Support interface | Speed of support interface. | `30`, `40`, `60` |
| `support_interface_top_layers` | integer | Top interface layers | Number of top interface layers. | `0`, `1`, `2`, `3` |
| `support_line_width` | float|percent | Support | Line width of support. If expressed as a %, it will be computed over the nozzle diameter. | `0.2`, `0.38`, `0.55` |
| `support_material_synchronize_layers` | boolean | — |  | `1` |
| `support_object_first_layer_gap` | float | Support/object first layer gap | XY separation between an object and its support at the first layer. | `0.3`, `1` |
| `support_object_xy_distance` | float | Support/object XY distance | XY separation between an object and its support. | `0.3`, `0.35`, `0.4` |
| `support_on_build_plate_only` | boolean | On build plate only | Don't create support on model surface, only on build plate. | `0`, `1` |
| `support_remove_small_overhang` | boolean | Ignore small overhangs | Ignore small overhangs that possibly don't require support. | `1` |
| `support_speed` | float | Support | Speed of support. | `150`, `40`, `60` |
| `support_style` | enum | Style | Style and shape of the support. For normal support, projecting the supports into a regular grid | `default`, `grid`, `snug`, `organic`, `tree_slim`, `tree_strong`, … (7 total) |
| `support_threshold_angle` | integer | Threshold angle | Support will be generated for overhangs whose slope angle is below the threshold. | `30`, `40`, `45` |
| `support_threshold_overlap` | float|percent | Threshold overlap | If threshold angle is zero, support will be generated for overhangs whose overlap is below the threshold. The smaller this value is, the steeper the overhang… | `50%` |
| `support_top_z_distance` | float | Top Z distance | The Z gap between the top support interface and object. | `0`, `0.1`, `0.2` |
| `support_type` | enum | Type | Normal (auto) and Tree (auto) are used to generate support automatically. | `normal(auto)`, `tree(auto)`, `normal(manual)`, `tree(manual)` |
| `symmetric_infill_y_axis` | boolean | Symmetric infill Y axis | If the model has two parts that are symmetric about the Y axis, | `0` |
| `thic_birdges` | boolean | — |  | `1` |
| `thick_bridges` | boolean | Thick external bridges | If enabled, bridges are more reliable, can bridge longer distances, but may look worse. | `0`, `1` |
| `thick_internal_bridges` | boolean | Thick internal bridges | If enabled, thick internal bridges will be used. It's usually recommended to have this feature turned on. However, | `0`, `1` |
| `timelapse_type` | enum | Timelapse | If smooth or traditional mode is selected, a timelapse video will be generated for each print. | `0` |
| `top_bottom_infill_wall_overlap` | percent | Top/Bottom solid infill/wall overlap | Top solid infill area is enlarged slightly to overlap with wall for better bonding | `15%`, `25%`, `35%` |
| `top_color_penetration_layers` | integer | — |  | `4`, `5`, `7` |
| `top_shell_layers` | integer | Top solid layers | This is the number of solid layers of top shell, including the top | `11`, `4`, `5` |
| `top_shell_thickness` | float | Top shell thickness | The number of top solid layers is increased when slicing if the thickness calculated by top shell layers is | `0.8`, `1`, `1.0` |
| `top_solid_infill_flow_ratio` | float | Top surface flow ratio | This factor affects the amount of material for top solid infill. | `0.96`, `0.97`, `0.98` |
| `top_surface_acceleration` | float | Top surface | Acceleration of top surface infill. Using a lower value may improve top surface quality. | `1000`, `2000`, `3000` |
| `top_surface_jerk` | float | Top surface | Jerk for top surface. | `5`, `7`, `8` |
| `top_surface_line_width` | float|percent | Top surface | Line width for top surfaces. If expressed as a %, it will be computed over the nozzle diameter. | `0.25`, `0.38`, `0.65` |
| `top_surface_pattern` | enum | Top surface pattern | Line pattern of top surface infill. | `monotonic`, `monotonicline`, `rectilinear`, `alignedrectilinear`, `concentric`, `hilbertcurve`, … (8 total) |
| `top_surface_speed` | float | Top surface | Speed of top surface infill which is solid. | `120`, `150`, `200` |
| `travel_acceleration` | float | Travel | Acceleration of travel moves. | `1000`, `10000`, `5000` |
| `travel_jerk` | float | Travel | Jerk for travel. | `10`, `12`, `8` |
| `travel_speed` | float | Travel | Speed of travel which is faster and without extrusion. | `100`, `350`, `400` |
| `travel_speed_z` | float | Z travel | Speed of vertical travel along z axis. | `0`, `12`, `15` |
| `tree_support_adaptive_layer_height` | boolean | — |  | `0`, `1` |
| `tree_support_angle_slow` | float | Preferred Branch Angle | The preferred angle of the branches, when they do not have to avoid the model. | `25`, `30`, `35` |
| `tree_support_auto_brim` | boolean | Auto brim width | Enabling this option means the width of the brim for tree support will be automatically calculated. | `0`, `1` |
| `tree_support_bramch_diameter_angle` | integer | — |  | `3`, `5` |
| `tree_support_branch_angle` | float | Tree support branch angle | This setting determines the maximum overhang angle that the branches of tree support are allowed to make. | `30`, `40`, `45` |
| `tree_support_branch_angle_organic` | float | Tree support branch angle | This setting determines the maximum overhang angle that the branches of tree support are allowed to make. | `35`, `40`, `45` |
| `tree_support_branch_diameter` | float | Tree support branch diameter | This setting determines the initial diameter of support nodes. | `2`, `5` |
| `tree_support_branch_diameter_angle` | float | Branch Diameter Angle | The angle of the branches' diameter as they gradually become thicker towards the bottom. | `10`, `15`, `3` |
| `tree_support_branch_diameter_double_wall` | integer | — |  | `0`, `1`, `3` |
| `tree_support_branch_diameter_organic` | float | Tree support branch diameter | This setting determines the initial diameter of support nodes. | `2`, `3`, `8` |
| `tree_support_branch_distance` | float | Tree support branch distance | This setting determines the distance between neighboring tree support nodes. | `10`, `5` |
| `tree_support_branch_distance_organic` | float | Tree support branch distance | This setting determines the distance between neighboring tree support nodes. | `1`, `2`, `5` |
| `tree_support_brim_width` | float | Tree support brim width | Distance from tree branch to the outermost brim line. | `0`, `3` |
| `tree_support_tip_diameter` | float | Tip Diameter | Branch tip diameter for organic supports. | `0.6`, `0.8`, `1` |
| `tree_support_top_rate` | percent | Branch Density | Adjusts the density of the support structure used to generate the tips of the branches. | `15%`, `20`, `30%` |
| `tree_support_wall_count` | integer | Support wall loops | This setting specifies the count of support walls in the range of [0,2]. 0 means auto. | `0`, `1`, `2` |
| `tree_support_with_infill` | boolean | Tree support with infill | This setting specifies whether to add infill inside large hollows of tree support. | `0` |
| `version` | string | — |  | `1.0.0.0`, `2.0.2.0`, `2.1.1.0` |
| `vertical_shell_speed` | percent | — |  | `80%` |
| `wall_count` | integer | — |  | `2`, `3`, `4` |
| `wall_direction` | enum | Wall loop direction | The direction which the wall loops are extruded when looking down from the top.  By default all walls are extruded in counter-clockwise, unless Reverse on even… | `auto`, `ccw`, `cw` |
| `wall_distribution_count` | integer | Wall distribution count | The number of walls, counted from the center, over which the variation needs to be | `1`, `2` |
| `wall_filament` | integer | Walls | Filament to print walls. | `1` |
| `wall_generator` | enum | Wall generator | Classic wall generator produces walls with constant extrusion width and for | `classic`, `arachne` |
| `wall_infill_order` | string | — |  | `infill/inner wall/outer wall`, `infill/outer wall/inner wall`, `inner wall/outer wall/infill` |
| `wall_loop_direction` | string | — |  | `clockwise` |
| `wall_loops` | integer | Wall loops | Number of walls of every layer. | `1`, `2`, `3` |
| `wall_sequence` | enum | Walls printing order | Print sequence of the internal (inner) and external (outer) walls. | `inner wall/outer wall`, `outer wall/inner wall`, `inner-outer-inner wall` |
| `wall_transition_angle` | float | Wall transitioning threshold angle | When to create transitions between even and odd numbers of walls. A wedge shape with | `10`, `25`, `59` |
| `wall_transition_filter_deviation` | percent | Wall transitioning filter margin | Prevent transitioning back and forth between one extra wall and one less. This | `25%` |
| `wall_transition_length` | percent | Wall transition length | When transitioning between different numbers of walls as the part becomes | `100%` |
| `wipe_before_external_loop` | boolean | Wipe before external loop | To minimize visibility of potential overextrusion at the start of an external perimeter when printing with | `0`, `1` |
| `wipe_on_loops` | boolean | Wipe on loops | To minimize the visibility of the seam in a closed loop extrusion, a small inward movement is executed before the extruder leaves the loop. | `0`, `1` |
| `wipe_speed` | float|percent | Wipe speed | The wipe speed is determined by the speed setting specified in this configuration. | `200`, `40`, `60%` |
| `wipe_tower_bridging` | float | Maximal bridging distance | Maximal distance between supports on sparse infill sections. | `10`, `2`, `5` |
| `wipe_tower_cone_angle` | float | Stabilization cone apex angle | Angle at the apex of the cone that is used to stabilize the wipe tower. | `0`, `10`, `15` |
| `wipe_tower_extra_flow` | percent | Extra flow for purging | Extra flow used for the purging lines on the wipe tower. This makes the purging lines thicker or narrower | `100%` |
| `wipe_tower_extra_rib_length` | float | Extra rib length | Positive values can increase the size of the rib wall, while negative values can reduce the size. | `0` |
| `wipe_tower_extra_spacing` | percent | Wipe tower purge lines spacing | Spacing of purge lines on the wipe tower. | `100%`, `120%`, `150%` |
| `wipe_tower_extruder` | boolean | — |  | `0` |
| `wipe_tower_filament` | integer | Wipe tower | The extruder to use when printing perimeter of the wipe tower. | `0`, `1`, `2` |
| `wipe_tower_max_purge_speed` | float | Maximum wipe tower print speed | The maximum print speed when purging in the wipe tower and printing the wipe tower sparse layers. | `120`, `200`, `50` |
| `wipe_tower_no_sparse_layers` | boolean | No sparse layers (beta) | If enabled, the wipe tower will not be printed on layers with no tool changes. | `0` |
| `wipe_tower_rib_width` | float | Rib width | Rib width is always less than half the prime tower side length. | `8` |
| `wipe_tower_rotation_angle` | float | Wipe tower rotation angle | Wipe tower rotation angle with respect to X axis. | `0`, `90` |
| `wipe_tower_wall_type` | enum | Wall type | Wipe tower outer wall type. | `cone`, `rib` |
| `wiping_volumes_extruders` | float[] | Purging volumes - load/unload volumes | This vector saves required volumes to change from/to each tool used on the | `70`, `70,70,70,70,70,70,70,70,70,70` |
| `xy_contour_compensation` | float | X-Y contour compensation | Contours of objects will expand or contract in the XY plane by the configured value. | `-0.05`, `0`, `0.02` |
| `xy_hole_compensation` | float | X-Y hole compensation | Holes in objects will expand or contract in the XY plane by the configured value. | `0`, `0.02`, `0.05` |
| `z_direction_outwall_speed_continuous` | boolean | — |  | `0`, `1` |


---

## Database Schema Suggestions

```sql
-- Core profile metadata (shared across all types)
CREATE TABLE profiles (
    id            INTEGER     PRIMARY KEY,
    type          TEXT        NOT NULL,   -- 'machine' | 'filament' | 'process'
    name          TEXT        NOT NULL UNIQUE,
    inherits      TEXT,                   -- references profiles.name
    setting_id    TEXT,
    from_source   TEXT,                   -- 'system' | 'user'
    instantiation BOOLEAN,
    description   TEXT,
    vendor        TEXT                    -- derived from directory name
);

-- Scalar settings (string / integer / float / boolean / enum / percent)
CREATE TABLE profile_settings (
    profile_id  INTEGER NOT NULL REFERENCES profiles(id),
    key         TEXT    NOT NULL,
    value       TEXT,                     -- always stored as text; parse by declared type
    PRIMARY KEY (profile_id, key)
);

-- Per-extruder / per-filament array values
CREATE TABLE profile_array_values (
    profile_id   INTEGER NOT NULL REFERENCES profiles(id),
    key          TEXT    NOT NULL,
    index_pos    INTEGER NOT NULL,        -- 0-based extruder/filament index
    value        TEXT,
    PRIMARY KEY (profile_id, key, index_pos)
);

-- Compatibility mapping: filament/process -> machine
CREATE TABLE profile_compatibility (
    profile_id   INTEGER NOT NULL REFERENCES profiles(id),
    machine_name TEXT    NOT NULL,
    PRIMARY KEY (profile_id, machine_name)
);
```

### Notes

- **JSON strings for all types:** OrcaSlicer stores every value as a JSON string, even numbers and
  booleans. Parse the value according to the declared type in this document.
- **Arrays:** Fields typed `integer[]`, `float[]`, `boolean[]`, etc. are stored as JSON arrays of
  strings. Use `profile_array_values` for a normalized schema, or a `TEXT` column for raw JSON.
- **G-code fields:** Fields ending in `_gcode` are multi-line G-code template strings — store as
  `TEXT` / `CLOB`.
- **Inheritance:** To resolve the effective value of a setting, walk the `inherits` chain until the
  field is found. Base profiles (`instantiation = false`) hold defaults only.
- **Percentages:** `percent` and `percent[]` values are stored as `"15%"`. Strip the `%` and divide
  by 100 when a ratio is needed.
- **Points:** `point` values use the format `"128x128"` (X×Y), not `"128,128"`.

---

*Generated from `resources/profiles/` and `src/libslic3r/PrintConfig.cpp`*
