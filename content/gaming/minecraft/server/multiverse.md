# multiverse

## skyblock in bukkit/spigot/paper

prerequisites:
- multiverse-core
- multiverse-inventories
- multiverse-netherportals
- voidgen

```
/mvcreate world_skyblock NORMAL -g VoidGen:{structures:true,decoration:true,mobs:true}
/mvcreate world_skyblock_nether NORMAL -g VoidGen:{structures:true,decoration:true,mobs:true}
/mvcreate world_skyblock_the_end NORMAL -g VoidGen:{structures:true,decoration:true,mobs:true}
```

create a multiverse-inventories group to share player data

```
/mvinv group
create
# name the group something like "world_skyblock"
world_skyblock  # add world
world_skyblock_nether  # add world
world_skyblock_the_end  # add world
@  # continue after adding all worlds
all  # share all player data
@  # finish
```

go to the world and change settings

```
/mvtp world_skyblock
/gm c  # go into creative, make a starter island
/mvsetspawn  #  set a reasonable spawn point
/mvmset respawnWorld world_skyblock  # respawn in overworld
/mvmset diff hard
/mvmset allowflight false
/mvmset alias Skyblock
/mvmset color AQUA

/mvtp world_skyblock_nether
/gm c  # go into creative, make a starter island
/mvsetspawn  #  set a reasonable spawn point
/mvmset respawnWorld world_skyblock  # respawn in overworld
/mvmset hidden true  # hide non-overworld from mvlist
/mvmset diff hard
/mvmset allowflight false
/mvmset alias Skyblock
/mvmset color AQUA

/mvtp world_skyblock_the_end
/gm c  # go into creative, make a starter island
/mvsetspawn  #  set a reasonable spawn point
/mvmset respawnWorld world_skyblock  # respawn in overworld
/mvmset hidden true  # hide non-overworld from mvlist
/mvmset diff hard
/mvmset allowflight false
/mvmset alias Skyblock
/mvmset color AQUA
```

if you use essentialsx-chat: go to `plugins/Essentials/config.yml` and `chat.world-aliases` and define your aliases

if you use essentialsx-spawn: set `respawn-listener-priority: lowest` so multiverse can handle respawns

### caveats

voidgen will generate water in ocean ruins, so if you want to play without water (ie getting rain in cauldrons) then this will be an easier way to get water

voidgen will also not let you control which structures spawn, only all or nothing. you can get golden apples and regular apples from certain loot chests

### todo

- apples are dropping from spruce trees in skyblock
- /spawn in test world takes you to survival
- per-world warps (so you dont pollute the whole list)
- check /weather permissions in skyblock
- check /time permissions in skyblock
- check /seed permissions in skyblock

i think a lot of perms are owner only but skyblock should remove them bc higher weight