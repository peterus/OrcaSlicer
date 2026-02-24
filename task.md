Analyze the bug that it failed to load project(3mf) from old version.
It failed pass below check in PresetBundle::load_config_file_config function, hence throw error.
    if (config.option("extruder_variant_list")) {
        //3mf support multiple extruder logic
        size_t extruder_count = config.option<ConfigOptionFloats>("nozzle_diameter")->values.size();
        extruder_variant_count = config.option<ConfigOptionStrings>("filament_extruder_variant", true)->size();
        if ((extruder_variant_count != filament_self_indice.size())
            || (extruder_variant_count < num_filaments)) {
            assert(false);
            BOOST_LOG_TRIVIAL(error) << __FUNCTION__ << boost::format(": invalid config file %1%, can not find suitable filament_extruder_variant or filament_self_index") % name_or_path;
            throw Slic3r::RuntimeError(std::string("Invalid configuration file: ") + name_or_path);
        }

RESOLVED: Added backward-compatibility safety checks in PresetBundle::load_config_file_config
(src/libslic3r/PresetBundle.cpp) immediately before the extruder_variant_list validation block.

Root cause: filament_self_indice was only initialized when the filament_self_index option was
completely absent, but was NOT resized when it existed with an incorrect (too small) size.
This caused the validation to throw a RuntimeError when loading old 3mf files or CLI-loaded
configs whose "inherits" parent presets could not be resolved.

Fix: Two safety checks were added:
1. If filament_self_indice.size() < num_filaments, resize it and fill with sequential 1-based
   indices (identity mapping) with a warning log entry.
2. If filament_extruder_variant exists but has fewer entries than filament_self_indice, resize
   it to match filament_self_indice.size(), defaulting new entries to "Direct Drive Standard",
   with a warning log entry.
