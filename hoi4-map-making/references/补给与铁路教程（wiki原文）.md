## Supply 

_See also:[Nudger § Supply](</Nudger#Supply> "Nudger")_

Starting positions of supply nodes and railways are defined within /Hearts of Iron IV/map/supply_nodes.txt and /Hearts of Iron IV/map/railways.txt respectively. **An invalid definition can cause crashes** when trying to open singleplayer or when trying to open the 'Supply' section in nudger. An invalid definition in this case is one that's going over non-existing or stateless provinces or a very disjointed railway definition.  
It is recommended to use the nudger's Supply section to assign supply nodes and railways. 

An entry in the supply nodes file has this formatting, without the semicolon: 
    
    
    Level; Province

The level represents the level of the supply node. By default, supply nodes have the max level of 1[11], so this is limited to 1.  
The province represents the ID of the province in which the supply node is located. Example entry is `1 1234`

An entry in the railways file has this formatting, without the semicolons: 
    
    
    Level; Amount of provinces; List of provinces

The level represents the level of the railway. By default, this is no more than 5.[12]  
The amount of provinces is how many provinces the railway lasts.  
The list of provinces is a whitespace character-separated list of province IDs on which this railway goes.  
A valid railway definition is the following: `4 4 693 1444 12 11`

### Supply areas (For versions prior to 1.11) 

**Note: With the release of 1.11 and No Step Back, supply areas are deprecated and instead the initial logistics/supply system is defined through supply_nodes.txt and railways.txt; see previous section. For further information on updating your map from 1.10 to 1.11, see[this post](<https://www.reddit.com/r/hoi4modding/comments/r2876d/updating_custom_map_mods_to_work_with_nsb/>).**

All states must be associated with a supply area. Each supply area can take any number of states, and each state should be in only one supply area. 

Supply areas are formatted as follows: 
    
    
    supply_area={
    	id=1
    	name="SUPPLYAREA_1"
    	value=12
    	states={
    		5 85 
    	}
    }
