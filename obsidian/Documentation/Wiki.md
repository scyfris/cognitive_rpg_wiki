# Overview of AGG Story Plugin

This Wiki will explain the core features of the AGG Story Plugin!

## Task Tracking


## Folder Structure

Right now the story is not a separate plugin but is part of the Rehab RPG repo at https://github.com/scyfris/RehabRPG.  The plugin resides in the AGGStoryPlugin folder, under `<root>/AGGStoryPlugin`.

### Icons

Contains various icons for game objects.

### Scenes

Contains pre-built scenes (i.e. module components) for Godot.  Which ones to use directly will be discussed later.  Some of these will be dragged into the scene by the user, others are components that aren't meant to be used directly.

### Scripts

Scripts for the various components and scenes.

## Creating a new Level

### Add autoloads.

Firstly, the plugin requires some autoloads.  In particular, the ones in this image:

![[Pasted image 20240201145436.png]]

All of these come from the AGGStoryPlugin directory, except for *DialogueManager* which is a third-party plugin.

### Create a new scene

Create a new directory for your game.  This should NOT be part of the AGGStoryPlugin dir.  In the following examples, our game will be stored under `<root>/Games/TutorialGame`.

Create a new 2D scene in that directory, deriving from Node2D (right click on your folder, go to New->Scene->2D Scene).  For this example, we will name it TutorialLevel1.  It should look like the following:

![[Pasted image 20240201145908.png]]

### Create TileMap and attach TileMapScene script to it.

The TileMapScene.cs script is located in `res://AGGStoryPlugin/Scripts/Components/TileMapScene.cs`.  This is the primary plugin script which serves as the glue for a lot of things.

Right click on the Root node in your new scene, and go to *Add Child Node*.  Type in "TileMap", and click Create.  It should now look like this:
![[Pasted image 20240201150246.png]]

After adding the TileMapScene.cs, the inspector should look like this:
![[Pasted image 20240201153431.png]]

For, now the TileMapScene properties blank - those will be discussed later.

#### Set Texture Filtering

By default the filter is "Inherit".  For pixel art, I like to use "Nearest" otherwise it might be a little blurry.
![[tileset setup 8.gif]]
#### Set Y-Sort on TileMap

You need to set the Y Sort Enabled on the TileMap itself, because its layers will be Y Sorted.  If you don't, you'll get a little red explanation mark.  It's under CanvasItem->Order->Y Sort Enabled

![[tileset setup 10.gif]]

#### Create your Tile Layers

For isometric tiles, each layer in the "up" direction needs to be a separate layer.  The first layer, the one specified in the TileMapScene's `Walkable Layer` is special because that is what we use to know the walkable surface for the player.

Every other layer above that is for decoration mainly.  But in order to get Y sorting and stuff worked out, we need to set things up a little special.  For all layers *other* than the ground layer, set Y Sort Origin to 16 more than the previous.  Set Y Sort Enabled to true on all of them.  And set Z Index to 0 for ground layer, and 1 for all other layers.

Here is example of what it should look like (note the layer names don't matter):

![[Pasted image 20240201155014.png]]

### Set up the TileSet

The first thing you need to do is set up your TileSet.  TileSets are outside the scope of this tutorial, for more info see https://docs.godotengine.org/en/stable/classes/class_tilemap.html.

AGG Story Plugin supports isometric and square tilesets.  For an example of an isometric tileset and tilesheet, please see AGGStoryPlugin/ExampleGames/TutorialGame/TileSets.  The *.tres* file is the TileSet.  The *.png* file is the image (exported from Aseprite or somewhere else).

**NOTE: Only use TileMap for objects that are static in the scene.  For objects that move, or other objects, we will set those up as separate objects.  So when setting up your TileSet, if your PNG tilesheet contains other objects that aren't terrain, don't set up tiles for them since we won't be painting them in the TileMap!**

Create the TileSet , and set the TileMap's Tile Set property.  See next section for how to create a TileSet if you don't already have one.  

**NOTE: Different TileMaps can share same TileSet.  If you are using a already-setup TileSet, skip to the next section.**


![[Pasted image 20240201153510.png]]

#### TileSet settings

These are the settings for an Isometric tileset using the AGG Plugin.  Note that we change almost all of the defaults.  In particular, Tile Shape should be Isometric, Tile layout is Diamond Down, Tile Offset Axis is Vertical Offset, and Tile Size is 32 x 16.



![[Pasted image 20240201151612.png]]

Later we will add a custom data layer to this, but for now that's it for the settings.
#### Create tileset tiles

For editing TileSets, it's on the bottom (make sure you are highlighting either the TileSet .tres file in the Folder view, or highlighting the TileMap in the scene hierarchy)

![[Pasted image 20240201151723.png]]

Drag the tilesheet PNG to it, **select NO** to automatically create tiles.

![[tileset setup 1.gif]]

Now you need to setup your tiles.  In the TileSet window, make sure "Setup" tab is selected, and the eraser are NOT selected (sometimes this confused me because eraser was selected and it wouldn't do anything).  The isometric tilemap is 32x16, and each region in the TileSet is 16x16.  Our tiels however, need to be full 32x32. So we need to select multiple regions at once.  In order to create a 32x32 tile, we must **HOLD SHIFT** and drag over each square tiles you want to merge.  See below

![[tileset setup 2.gif]]

#### Set tile properties

To finish with the TileSet setup, you need to do just a couple more things.

##### Set Texture Origin on each tile.

This basically shows where sprite will lay compared to the ACTUAL isometric diamond tile.  What I will do is this:

* For ground tiles, make sure the diamond is set to the TOP of the tile.
* For tiles that sit on the ground, and all other levels above - make sure the diamond is set to the BASE of the tile.
See below for how I set these (and see example in the IsometricApartmentTileSet in the Tutorial example.).  In our example, the floors get a `Texture Origin` of `y==3` for the floors, and `y==8` for the blocks.  This puts the isometric tile face on the top of the floor, and at the base of the blocks that sit on the floors.

![[tileset setup 3.gif]]

##### Set Y Sort Origin on each tile

I don't fully understand this, but for now set the `Y Sort Origin` to the middle of the top face of the each tile.  See below (I set -16 for the big chungus to make the little dot in the center of the top face.  for the flatter tiles, the dot is already in the center of the top face when it is set to 0, so I leave them alone.):
![[tileset setup 6.gif]]

##### Set "blocked" custom data layer

In a game, will want certain terrain on top of the ground to block the players walk.  AGG Story Plugin has a solution for this!

This requires you to set the a custom data layer on both the TileSet, and let the TileMap know what it is.  By default, this layer is named `blocked` and is expected to be a `bool`.  You shouldn't have any reason to change it, but just FYI it's on the TileMapScene inspector:

![[Pasted image 20240201153749.png]]

You need to manually set `blocked` on the tileset tiles you want to block player.  This should NOT be any of the floor tiles, but could be anything sitting on top of the floor.  **Note: This is only for static tiles in the tilemap.  For objects we place in the scene, we use TileFootprints to block players.  That will come later**

Here's how:

In the TileSet settings (in inespector), set a new Custom Data Layer and name it "blocked"

![[tileset setup 5.gif]]



### Test out the tile map

You should now be able to paint your tiles.  Note that you need to make sure to choose the proper level.  Below GIF shows example of painting!

![[tileset setup 9.gif]]

### Add required Components

For every level, there are multiple required components.

#### Globals (autoloads)
These components should be added to your Autoload in your project, and are located under res://AGGStoryPlugin/Scenes/Systems/Required/Globals

* GameStateManager.tscn
* LevelManager
	* Provides support for loading different levels/scenes.
* MinigameManager
	* Provides hooks to and from minigames.
* SceneActionManager
	* Responsible for cutscenes initiated from Dialosgue.
* GlobalEventBus
	* Responsible for passing events to components!

You must also have
* Dialogue Manager (external plugin)

#### Per-scene reqiored components
* Camera2D.tscn
	* Provides a camera viewport for the scene.
* Fader.tscn
	* A fader class for the CutsceneAction fader class.
	* These can also be accessed from ActionResourceSetFader.
* TileMapComponents.tscn
	* Contains Input functionality for the tilemap, including debug drawing for path planning.
* TileMapEntrance
	* Every scene needs 1! 
* TileMapPlayerController
	* The actual character.


### Add Playercontroller

### Add TileObjects

Tileobjects are pre-built scenes that represent a clickable/interactable area in the map, or an area that takes up space in the map (i.e. the player can't walk through it).  These are not static.

TileObjects are located under res://AGGStoryPlugin/Scenes/Systems/TileObject/TileObject.tscn

TileObjects will also need TileFootprints if they are blocking the player's walking space (these are located under res://AGGStoryPlugin/Scenes/Components/TileFootprint.tscn ).  Just drag those on, you should see the tile highlighted that it takes up.

##### TileObject parameters

* `Blocks Path` - If true, the player can not walk through the tile taken up by this TileObject.  Requires a TileFootprint.
* `Clickable Collision Object (optional)` - A CollisionShape2D object representing a clickable region.
* `Enable Click Actionable (optional)` - The click will trigger an action defined in the Click Actionable Resource field.
* `Click ActionableResource (optional)` - The action to execute if clicked on and Enable Click Actionable is true and the object has a Clickable CollisionObject.
* `Click Actionable Optional Target Point` - The point the player should walk to if "require walk to perform" is on.  By default, if this is empty, the player will walk to the Position of the actionable.
* `Click Actionable Require Walk To Perform` - True if the player needs to walk to TileObject first, or if it's instance after clicking.

##### TileObject Sprites

TileObjects don't need sprites, they could just be clickable regions.  But it's good to have one if this is a real object...

Just add a Sprite2D or other AnimatedSprite to it.
![[Pasted image 20240203154631.png]]
### Add Dialogues

#### Add new dialogue balloons

You can create your own dialogue balloons with Dialogue Manager pretty easily.  We have a script `AGGDialogueBalloonPortrait` that can be used to create portrait-style balloons.

![[test_portrait.gif]]

After adding it, open it up.  Remove and delete the DialogueBalloon.cs script that came with it, and add on the `AGGDialogueBalloonPortrait` script.

![[Pasted image 20240207161204.png]]

Set the "Portrait Folder" property to point to a folder containing your portrait images.  These need to be PNGs right now, and the base name of the file should be the same as the character's name in the dialogue (ToLower() is used so it should be lower-case).

For example, for a character named **Kurisu**, it would be:
![[Pasted image 20240207161400.png]]

![[Pasted image 20240207161425.png]]

You must also make sure to put a node named "Portrait" somewhere onto the balloon, otherwise you will get a runtime error.
![[Pasted image 20240207162657.png]]
#### Tags

`[#thought]` A thought dialogue. (text will be enclose with parenthesis)
`[#noportrait]` - Disable portrait on dialogue.
 
By default, dialogue will display with "" around it.  Thought dialouges display with ().  Dialogues with no character (narration) display as 

### Add Cutscenes

Cutscenes are events you can trigger from your Dialogue scripts, by making calls into CutsceneActionManager.TriggerCutsceneAction(`<string name of cutscene node or cutscene group node in the scene>`)

All of the premade cutscene nodes are under AGGStoryPlugin/Scenes/Systems/Cutscenes.  All the options are configurable through the inspector.

`Is Blocking` On all the cutscene types will force the Dialogue Manager to wait before continuing.
#### Cutscene types

##### CutsceneActionStopWalking

Stops the player from walking.

##### CutsceneActionWait

Waits for N amount of seconds.

##### CutsceneActionWalk

Triggers an actor to walk to a target.

##### CutsceneActionFader

Fades in or out using the scene's "Fader" component.  

#### MISC

* Walkable levels of the TileMap are only from the level specified in `TileMapScene::Walkable Layer`.
*#### Autoloads/Globals

LevelManager
* Contains functionality for loading different levels/tilemaps and initializing those maps.
	* LoadLevel() - Primary function to load a new tilemap scene.  Specify the level enum and an optional entrance name (must correspond to the name of a TilemapEntrance component somewhere in that tilemap.)

#### Tilemap Components

* Actionable
	* This class can be attached to an object to perform some action.  Typically, the object this is attached to will also have some class like "Clickable" whose signal would be hooked up to Actionable::Action().  This class won't do anything without a signal or other code executing Action().  The action that gets executed must also be set to the _action_ member via the Godot inspector.  The action contains the actual action function.
		* ActionResource
			* This is an actual action function.  There are different kinds, browse the subclasses of ActionResource.
		* Actionables can be clicked - their `sort order` affects overlapping, so be careful.
* TilemapEntrance
* TileMap references:
	* Reddit discussion with some useful info on how many tiles needed
		* https://www.reddit.com/r/godot/comments/xzeovf/how_would_you_autotile_this_pulling_my_hair_out_d/
	* Another reddit post, referring to the name of these type of tiles: Wang or Blob tile sets.
		* https://www.reddit.com/r/godot/comments/u6f0v4/what_tiles_do_i_need_for_each_autotile_bitmask/
	*  A git issue post that describes all the different combinations of tiles for autotiling!
		* https://github.com/godotengine/godot-docs/issues/3316
	* TilePipe -  A tool to help make tileset generation easier
		* https://aleksandrbazhin.itch.io/tilepipe2
	* 3x3minimal:
		* ![[Pasted image 20240117113859.png]]


#### SceneActionManager and Cutscenes

#### Actor Animation names

These are requirements for actors that move left/right/down/up and can remain idle.
walk_down
walk_up
walk_side
walk_side (reversed)
default (idle)

Optional:
surprised - trigged by cutscenes

### Workflow


Minigame

# Overview of Passing Time

This section details specifics of working with the game Passing Time.

### Create Minigame


We have multiple Minigame prototypes available, you should not need to roll your own. ( we don't want to either, as we will have maybe 100's of minigames , imagine debugging all of them independently).  Instead we have a few minigame prototypes, and the data is the only thing that changes between minigames.

#### Base Setup
The basic steps to define a new minigame are:

1) Create a new Minigame scene under the `Games/PassingTime/Maps/<Chapter>/<Location>/Minigames`  folder.  Make sure it is of type `Node` or `Node2D`.
	1) Set the root node as a "Minigame" script derived from one of the Minigame prototype scripts (see Games/PassingTime/Assets/Minigames/Prototypes/MemoryRecall/MemoryRecallMinigame.cs as one such example)
2) Create a MinigameDef under your `Maps/<Chapter>/<Location>/Minigames/<minigame subfolder>`
	1) Give it a name
	2) Give it a path to a scene with a Node (Minigame script) as root , created from step 1-2.
3) If using this in an Actionable, create a ActionResourceStartMinigame - Give it a reference to your minigame def from step 3, along with the dialogue entry points to the Pass Dialogue and Fail dialogue.
4) Assign your ActionResourceStartMinigame to some Actionable, or trigger from cutscene.
5) Make sure to drag in FinishButtonControlExample.tscn into the scene for the finish button (or provide your own by overriding in the root MemoryRecallMinigame.cs script.

That's it for the basic setup - you defined a minigame!
See below for filling out the minigame scene (depending on the minigame prototype, the instructions are different.)

#### Minigame Prototype speific information

All the prototypes can be found in Games/PassingTime/Assets/Minigames/Prototypes.  This section details steps to build a new minigame using these.

##### Prototypes/MemoryRecall

Set your root node script to be MemoryRecallMinigame.cs.

This minigame type has the following 3 phases:
1) Memorization phase - Give the player X seconds to memorize images on the screen.
2) Guess phase - Allow the player to guess what they saw from memory.
3) Scoring phase - Show the player feedback on what they got wrong or right.

All of these phases make use of "Answers" defined in Prototypes/MemoryRecall/MemoryRecallAnswerProto.tscn.

###### Set up the Answer Pool

**First** - Drag as many Prototypes/MemoryRecall/MemoryRecallAnswerProto.tscn into the scene as you want.  
Make sure to **drag them outside of the viewing area** because these won't actually be the instances the player sees.
Feel free to change the sprite associated with this if you would like.  Note - there is a clickable region , you must redefine that if your sprite is larger or smaller than the example sprite (32x32 pixels).

**Second** - Set the root "MemoryRecallMinigame.cs" property `MemoryRecallMinigame` in the inspector to fill in the "Answer Pool" with all of your objects.

###### Set up the locations for the Memorization Phase

During the memorization phase, a select number of items from the answer pool will be displayed to the player to memorize.  You must tell the minigame where the locations should be.

**First** - Create a few Node2D's in the scene (same amount as answers).

**Second **** - Set up the root "MemoryRecallMinigame.cs" `MemorizationPositionsToInitializeAt` to point to all your positions.

**Third** - Set up the `Num Answers` and `Num Seconds To Guess` parameters.  `Num Answers` is how many need to be memorized.  `Num Seconds to Guess` is how long the layer gets to guess.

###### Set up the locations for the Guess phase.

**First** - Similar to the Memorizsation phase, create a bunch of Node2Ds for guess phase.  This needs to be GREATER THAN OR EQUAL TO the amount of answers in the answer pool (to allow for the hardest difficulty).

**Second** - Also set the MemoryRecallMinigame.cs parameter.

###### Set the correct and incorrect UI marks

This can be done by setting the root's `Ui correct answer texture` and `Ui Incorrect Answer Texture` to the `PassingTime/Assets/Minigames/UI/CheckMark.png` and ``PassingTime/Assets/Minigames/UI/XMark.png`` respectively.  Or you can set your own.




###### Optionally override the finish button

On the root, make sure you have dragged in `PassingTime/Assets/Minigames/Prototypes/MemoryRecall/FinishButtonExample.tscn".`  You can create a `Control` node if you want to attach it to.

You MUST name the finish button "FinishButton" or as unique name "%FinishButton".

**IMPORTANT NOTE**
You **must** select `Access as Unique Name ` from menu and name it " `FinishButton` " Otherwise there will be error that %FinishButton couldn't be found.

If you want to override it, then set `Finish Button Name Override` to `%FinishButton`.


### Blender

* Export as *.gltf*
* Export animations merged together*
	* ![[Pasted image 20240417114047.png]]
* Create inheritted scene in Godot
* Rebuild shaders

### Save Variables

#### Variables used in scenes

Variables prepended with GLOBAL persist outside of the scene in which they are used.
Variables not prepended will only persist within the dialogue referencing them.  This way, when a location loads, and the corresponding scene's dialogue is referenced, the variables are self-contained and don't get referenced outside of the scene.

#### Global variables

These variables are referenced globally.  An example of a variable like this is `GLOBAL_ALEX_CLOTHES`.  The `AlexPlayerController` references this variable to determine if Alex should be earing his lab coat or not (i.e. if `GLOBAL_ALEX_CLOTHES` == `LAB_COAT` or not)

#### How to save and load variables in code

We use GameStateManager.SetValue and GameStateManager.GetValue to save and load variables.  

##### Example: From Dialogue

```# Set up Alex
	if (GameManager.QuestIsFinished("Chapter1/Scene1/GetDressed"))
		do GameStateManager.SetValue("ALEX_STATE_LABCOAT", true, true)
	else
		do GameStateManager.SetValue("ALEX_STATE_LABCOAT", false, true)
```


### AnimatedSpriteMutator

Used to select sprite frames from global variables.  See `AlexPlayerController` for example.


### Quests

#### How quests work

The player can be given quests.  In-game actionables can be disabled or enabled depending on if a quest is active - for example, a "Make Coffee" quest would enable the player to click on the coffeepot actionable to open up the coffee minigame.

Quests can also have prerequisites, so even if the player has the quest, if the prerequisite quests aren't met then the Actionable will do nothing (and optionally will play a dialogue indicating to the player they haven't completed something yet).

Quests can be checked in both C# code as well as the Dialogue files.

Below is an overview of all the technical stuff behind quests.
#### Quests folder
The quests are defined as resources of type `Quest.cs`.  These reside in the quest folder specified on the global autoload GameManager.  For example, all the quest definitions for scene 1 are under res://Games/PassingTime/Assets/Quests/Chapter1/Scene1/ . 

#### Quest.cs Resource type

This resource defines a quest and prereqs.  The `Quest Name Unique` is a name stored in the database when saved.  `Friendly name` is a string that can be displayed in-game, like when player hovers over the quest or something.  `Prereqs` specify other quests that are required to be complete before this quest can be completed - this is mainly used by the Actionable to provide dialogue to the player if they try to click on a quest and the prereq is not met (such as trying to check emails before getting coffee).

#### Quest Actionable properties

Actionables have a few properties to interact with quests.

##### Actionable Quest Required

Determines whether the player needs to have the quest active or not in order to call this actionable.  This takes a Quest.cs resource as above.

##### Quest Prereq Not Met Dialogue

Optional dialogue to play if the quest's prereqs have not been obtained.

##### Quest Already Finished Dialogue

Optional dialogue to play if the quest is already finished .

##### Quest Not Yet Have Dialogue

Optional dialogue to play if the player doesn't have the question yet.

#### GameManager functions for dealing with Quests

Quests are handled differently from normal save variables.  That is because quests are defined in Resources of type `Quest.cs` and are located in a base folder that is specified to the global GameManager.  The game manager can be interacted with from dialogue files or code files.
##### GameManager.ChkQuest()
You can verify a quest file exists by calling `GameManager.ChkQuest`.  This can be done in code or in Dialogue files.  This will force an error if the quest file does not exist.

Example from the Dialogue file:

```
# Check our quests so we error out very quickly if there is something wrong.
	do GameManager.ChkQuest("Chapter1/Scene1/GetDressed")
	do GameManager.ChkQuest("Chapter1/Scene1/CheckEmails")
	do GameManager.ChkQuest("Chapter1/Scene1/MakeCoffee")
```

##### GameManager.QuestGive()

You can give a quest to the player.

Example of dialogue file:

```
# Give the first quest line.  Note that not all quests will be active - that depends on the dependency .
do GameManager.QuestGive("Chapter1/Scene1/MakeCoffee")
```
##### GameManager.QuestHas()

This checks if a player currently has the quest.

##### GameManager.QuestIsFinished()

Returns true if the quest as finished, false otherwise.

```
# Set up Alex
	if (GameManager.QuestIsFinished("Chapter1/Scene1/GetDressed"))
		do GameStateManager.SetValue("GLOBAL_ALEX_STATE_LABCOAT", true, true)
	else
		do GameStateManager.SetValue("GLOBAL_ALEX_STATE_LABCOAT", false, true)
	
```
##### GameManager.QuestSetFinished()

Sets the quest as finished.

```
do GameManager.QuestSetFinished("Chapter1/Scene1/GetDressed")
```









