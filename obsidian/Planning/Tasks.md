This lists tasks we need to do.

https://trello.com/b/zADLZojt/rehabrpg

## Sprint Meeting 2/9/24


## Week of 2/9/24
* TASKS:
	* Design week-by-week plan for completion of pilot
		* Week 2/5-2/9:
			* WRITING/DESIGN - Complete Pilot scene 1.  Include dialogue, set pieces, and minigames .  See minigame design taks below.
			* ART - Start designing apartment level and set pieces.
	
	* Begin documentation how to make levels and stuff in Godot for our game, and how the code works so both Billy and Mary can work on things.
		* 2/3/24 - Billy began documentation, aim to be complete by EoW.
		

	* Determine how to start communicating to customers (personal like Christina, but also public facing).  Basically work on strategy for update pipeline.
	
	* Communication
		* Only two we really want to update are Christina or Jeramiah.  
			* Maybe email, maybe subscription on website for updates on games (newsletter).
			* Maybe 2-3 updates for the pilot on stroke reddit page, after we have gotten further along.
				* has therapeutic component behind it, on surface looks like normal game, etc . Use this to gauge interest, not necessarily feedback.
					* Need a metric - follows, wishlist, etc.
					* Initiial 
					
	*  Need to figure out how we want to structure minigames
		* **Option A**: Each minigame in the game is a "slot", and one of N variations of the minigame will be played based on what the player set up in the begging (memory, spatial, reading, etc)
		* **Option B**: We have different minigames at different times depending on what the player set up in the beginning.
			* Ex: Player wants to focus on Memory.  We determine we have X amount of minigames in Pilot.  We determine when the memory minigame is played.  If the player had chosen spatial, then that minigame may be played at a different time in the story (where the spot where the memory game would have been played is filled with dialogue and no minigame is played then)
		* **Option C**: Single minigame, at a time T in the game.  All players play the same type of minigame, they don't set things up.  This is the most straightforward/linear approach, but doesn't let players custom tailor the game to their specific rehab needs.
	
	* Experiment with overall palette/art
		* Start designing apartment with isometric tiles.  Use a palette we like at first.  We can change it later, but for now it will let us iterate.
# Meeting Notes
## Bringup

 **Goal**: Get us to where we can focus on story and content.  Get all infrastructure stuff out of the way.  At the end of bringup, we should have all the tech and know-how to make the real game.
 
**Features Still Pending**
* Inventory system (for puzzles)
	* Draggable inventory to objects on screen (to activate something).
* Skill system integration with dialogue and such
* Figure out process for making isometric art
	* Tools (tiled vs in-godot editor)
	* Aseprite
		* How to do isometric art efficiently.
	* Parameters of iso
		* width/height of cells, etc.
		* Practice making it.
	* Lospec
		* For color palettes

## Planning

### Sprint 2/2/24 - Finish Bringup

The goal of this sprint is to finish converting existing unit test to isometric and nail down our process prior to starting on the real pilot.  We should also have a more concrete understanding of how the pilot's first parts will be so that Billy can plan out a schedule/roadmap based on what is needed.

#### Sprint Tasks

* ~~Finish Unit Test, Finish converting to Isometric~~
	* ~~Art converted to Iso~~
		* ~~Items:~~
			* ~~Walls - B~~
			* ~~Floor - B~~
			* ~~Fridge - M~~
			* ~~Whiteboard - B~~
			* ~~Clock - B~~
			* ~~Calendar - M~~
			* ~~Fish-shaped thing next to Calendar - B~~
			* ~~Couch - M~~
			* ~~Table - B~~
			* ~~Fridge - M~~
			* Whiteboard - B
			* ~~Clock - B~~
			* ~~Calendar - M~~
			* Fish-shaped thing next to Calendar - B
			* ~~Couch - M~~
			* Table - B
			* ~~Upa - B~~
			* ~~Door with Curtain - B~~
			* ~~Stove/sink area. - M~~
		* Characters:
			* ~~Okabe~~
				* ~~Just laying down sprite needed.~~
			* Kurisu
				* Need walking forward at 45degree and walking backward at 45 degree.
	* ~~All functionality (walking , clicking for dialogue, etc) converted to Iso~~
	* ~~Workflow decided (Tiled vs in-Game godot)~~
		* ~~Look at our infrastructure again to make sure we can't use Tiled.~~
		* ~~Document the workflow.~~
* Design week-by-week plan for completion of pilot
	* This is a management task.  Deadline to have schedule is 2/2/24.
* Begin documentation how to make levels and stuff in Godot for our game, and how the code works so both Billy and Mary can work on things.
* Determine how to start communicating to customers (personal like Christina, but also public facing).  Basically work on strategy for update pipeline.

* **ACTION ITEMS**
	* We've decided to stick with Godot tile editor for now.
		* Teach Mary how to use TileMapper in Godot.
		* Document workflow
	* Communication
		* Only two we really want to update are Christina or Jeramiah.  
			* Maybe email, maybe subscription on website for updates on games (newsletter).
			* Maybe 2-3 updates for the pilot on stroke reddit page, after we have gotten further along.
				* has therapeutic component behind it, on surface looks like normal game, etc . Use this to gauge interest, not necessarily feedback.
					* Need a metric - follows, wishlist, etc.
					* Initiial 



## Finished

* ~~Transition between scenes.~~
* ~~Animations (idle, walk, etc)~~

.
* ~~Dialogue manager integration (Articy? Godot DM?)~~
	* We are going to use Godot Dialogue Manager.
* ~~Minigame transition.~~
* Art:
	* Lospec.com
	* Figure out how to make tilesets (take some tutorials)
	* Make a character
* ~~Walk around~~
	* 1/8/24 - Got this working with wasd
	* TODO: Get working with clicks (path planning based on collisions).
		* Found this Subreddit and link
			* Subreddit: https://www.reddit.com/r/godot/comments/13vbcck/point_and_click_movement_in_grid_pattern/
			* Link: https://www.gdquest.com/tutorial/godot/2d/tactical-rpg-movement/
		* For tilemap navigation data:
			* https://www.reddit.com/r/godot/comments/zxmqte/navigation_on_tilemap_in_godot_4/
			* MUST READ for setting collision avoidance on the path planning properly.  IT's for 3.5, but still lead me to find the problem (hint cell size!): https://www.reddit.com/r/godot/comments/x7s4z1/guide_for_how_to_fix_navigationagents_getting/
	* 1/9/24 - Okay, I instead switched to AStarGrid as the whole collision detection and AStar algorithm was a bit messy (I would have to account for the NavigationAgent's size somehow which Godot's built-in navigation stuff doesn't account for).  So instead, we are doing away with the collision detection and AStar stuff and going with AStarGrid.  The tilemap's custom data has a "blocked" boolean which will set a tile cell as offlimits.  I've been needed to adjust the Texture Offset and Y Sort origin for certain tiles thogh to make sure things work well, but now it works good.
		* **TODO** - Clean up code and document process for tilemap.
		* **TODO** - Snap to grid for things like tilemap entrance?
		* **TODO** - Investigate if Tiled should be used?  Or just move straight to the dialogue system.
			* Explainer of Y-Sorting: https://www.youtube.com/watch?v=UdmD_2EcEgU
			* Tiled 1.10.2 now has native Godot export to Godot's tilemap format as a scene ([https://doc.mapeditor.org/en/latest/manual/export-tscn/](https://doc.mapeditor.org/en/latest/manual/export-tscn/))  I tried it , I think a couple major things I didn't like about it and am choosing to go with Godot's built-in for my game:
				* Defining custom tile set data layers was easy , I could specify it in Tiled, and the export would create a Godot Tilemap with that data layer. BUT I didn't see a way to specify _built-in tile data_ (like per-tile Y Sort Origin or Z Index). That was really important for me, so I couldn't use it.
				* Every time I save it overwrites the tilemap in Godot - so custom settings like my CanvasItem::Texture::Filter on the object gets reset ! (I like it set to nearest for pixel art).
				* This video states some potential problems with Godot 4's tilemap system:
					* https://www.youtube.com/watch?v=9HMIbGLLrSk
    

* ~~Collision detection for obstacles.~~
	* 1/8/24 - Got this working.
* Tilemap workflow
	* ~~Need to figure out how to make character appear "behind" layers.~~
* ~~Clickable regions for dialogue.~~
	* Need to be able to click on objects and walk there.  Get that working with the path system.
		* InputEvent tutorial shows ordering for _input, unhandled_nput, and CollisionObject._input_event() !: https://docs.huihoo.com/godotengine/godot-docs/godot/tutorials/engine/inputevent.html
		* **TODO**: Figure out how to add those cool yellow explanation marks for scene dependencies!
* Nice picture: https://diogo-vernier.itch.io/animated-character-16-x-16
* pokemon sprites: https://www.youtube.com/watch?v=gwF0L55kIgg&t=464s
* aseprite animation tutorial: https://www.youtube.com/watch?v=B0enS9BJne4
*
### UnitTest

* ~~Come up with a list of cutscene actions.  Inspiration from https://www.youtube.com/watch?v=DLBJpapM9xs~~
	* CutsceneAction
		* Start Dialogue
		* Move Actor
		* Trigger Animation
		* Etc.
* ~~Have a global EventManager~~
* ~~DialogueManager can trigger cutscene sequences - basically a chain of CutsceneActions~~
* CutsceneAction:
	* ![[Pasted image 20240118184041.png]]



#### When thinking about Saving
* Look at this: https://www.reddit.com/r/godot/comments/t8stwm/save_game_state/




#### Patterns
https://refactoring.guru/design-patterns/catalog
https://gameprogrammingpatterns.com
## Proto 1
* Make a game based on the Pilot's first part (Alex in his house waking up).  These will exercise:
	* Opening sequence/cinematic
	* Minigame initiation / interface 
	* Animation/sequences (dialogue, visual, etc)
	* Tilemap for apartment - workflow to create tiles.
	* Camera following player
