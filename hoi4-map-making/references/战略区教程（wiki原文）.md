This is a community maintained wiki. If you spot a mistake, please help with fixing it. 

[Strategic regions](</Strategic_Region> "Strategic Region") are defined in /Hearts of Iron IV/map/strategicregions/*.txt. 

Each strategic region is typically stored in it's own file, although you can store multiple strategic region definitions within the same file, as the ID is defined within the strategic region definition, rather than the file title. 

Here is a generic example of a strategic region: 
    
    
    strategic_region = {
        id = <int>
        name = <localization key>
        
        provinces = {
            <province ids>
        }
        
        weather = {
            period = {
                between = { <min> <max> }
                temperature = { <min> <max> }
                no_phenomenon = <weight>
                rain_light = <weight>
                rain_heavy = <weight>
                snow = <weight>
                blizzard = <weight>
                mud = <weight>
                sandstorm = <weight>
                min_snow_level = <amount>
            }
        }
    }
    

  * **id** defines the numerical id used by the strategic region. The strategic region IDs must be added sequentially, skipping numbers will cause crashes.

  * **name** defines the localization key the strategic region uses. You can use a non-localized string (i.e. "Paris"), but it is best practice to use localized strings.

  * **provinces** scope defines which provinces the strategic region is composed over.

  * **weather** scope determines the weather within the provinces covered by the strategic region.

## Weather 

|  This is a community maintained wiki. If you spot a mistake, please help with fixing it.  
---|---  
  
Each strategic region has a **weather** scope that determines how the weather changes for provinces within it. 

Each weather system is defined within a **period** scope within the **weather** scope. 

  * **between** scope determines when the weather system occurs, the notation is `day.month day.month`, i.e. `0.0 30.0` means the weather system occurs between the 1st of January and the 31st, including these days. Note that the first day and the first month are marked as 0, not as 1.

  * **temperature** scope determines the minimum and maximum temperature for the weather system.

  * ~~**temperature_day_night** scope determines the minimum and maximum temperature variability during day and night for the weather system.~~ (This is no longer used since version 1.11, set all temperature ranges via temperature)

  * **min_snow_level** scope determines the minimum amount of snow that is always present in the weather system. Typically only used for areas with year-round snow.

Each of the weather states are given a weight, determining how likely the state will occur within the weather system. The weather states can be found in /Hearts of Iron IV/common/weather.txt. 

## Tips 

|  This is a community maintained wiki. If you spot a mistake, please help with fixing it.  
---|---  
  
  * When placing strategic regions, the provinces they are compose of should be contiguous. This means islands are normally part of a _sea_ strategic region.

  

**[Modding](</Modding> "Modding")**

Documentation  | [Effects](</Effect> "Effect") • [Triggers](</Conditions> "Conditions") • [Defines](</Defines> "Defines") • [Modifiers](</Modifiers> "Modifiers") • [List of modifiers](</List_of_modifiers> "List of modifiers") • [Scopes](</Scopes> "Scopes") • [Localisation](</Localisation> "Localisation") • [On actions](</On_actions> "On actions") • [Data structures](</Data_structures> "Data structures") ([Flags](</Data_structures#Flags> "Data structures"), [Event targets](</Data_structures#Event_targets> "Data structures"), [Country tag aliases](</Data_structures#Country_tag_aliases> "Data structures"), [Variables](</Data_structures#Variables> "Data structures"), [Arrays](</Data_structures#Arrays> "Data structures"))   
---|---  
Scripting  | [Achievements](</Achievement_modding> "Achievement modding") • [AI](</AI_modding> "AI modding") • [AI focuses](</AI_focuses> "AI focuses") • [Autonomous states](</Autonomy_state_modding> "Autonomy state modding") • [Balances of power](</Balance_of_power_modding> "Balance of power modding") • [Bookmarks/Scenarios](</Bookmark_modding> "Bookmark modding") ([Game rules](</Bookmark_modding#Game_rules> "Bookmark modding")) • [Buildings](</Building_modding> "Building modding") • [Characters and traits](</Character_modding> "Character modding") • [Cosmetic tags](</Cosmetic_tag_modding> "Cosmetic tag modding") • [Countries](</Country_creation> "Country creation") • [Divisions](</Division_modding> "Division modding") • [Decisions](</Decision_modding> "Decision modding") • [Doctrines](</Doctrine_modding> "Doctrine modding") • [Equipment](</Equipment_modding> "Equipment modding") • [Events](</Event_modding> "Event modding") • [Factions](</Faction_modding> "Faction modding") • [Ideas](</Idea_modding> "Idea modding") • [Ideologies](</Ideology_modding> "Ideology modding") • [Military industrial organizations](</Military_industrial_organization_modding> "Military industrial organization modding") • [National focuses](</National_focus_modding> "National focus modding") • [Resources](</Resources_modding> "Resources modding") • [Scripted GUI](</Scripted_GUI_modding> "Scripted GUI modding") • [Technologies and doctrines](</Technology_modding> "Technology modding") • [Units](</Unit_modding> "Unit modding")  
---|---  
Map  | [Map](</Map_modding> "Map modding") • [States](</State_modding> "State modding") • [Supply areas](</Supply_areas_modding> "Supply areas modding") • Strategic regions  
---|---  
Graphical  | [Interface](</Interface_modding> "Interface modding") • [Graphical assets](</Graphical_asset_modding> "Graphical asset modding") • [Entities](</Entity_modding> "Entity modding") • [Posteffects](</Posteffect_modding> "Posteffect modding") • [Particles](</Particle_modding> "Particle modding") • [Fonts](</Font_modding> "Font modding")  
---|---  
Cosmetic  | [Portraits](</Portrait_modding> "Portrait modding") • [Namelists](</Namelist_modding> "Namelist modding") • [Music](</Music_modding> "Music modding") • [Sound](</Sound_modding> "Sound modding")  
---|---  
Other  | [Console commands](</Console_commands> "Console commands") • [Troubleshooting](</Troubleshooting> "Troubleshooting") • [Mod structure](</Mod_structure> "Mod structure") • [Mods](</Mods> "Mods") • [Nudger](</Nudger> "Nudger")  
---|---
