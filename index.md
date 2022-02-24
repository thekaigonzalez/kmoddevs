[TOC]



# KMOD Developer Manual

"Kai's Modular Interface" (Formerly KMODG) is a Sandbox user-generated experience written in the GODOT engine. This contains the basics you need to create mods. And this comes with KMOD.



## 1.0 - Managing Scripts

This will show you how to begin your journey with creating Mods for KMOD.

KMOD uses Godot Engine for it's core scripting, with an external Mod API referencing multiple handlers & Overrides (since 0.0.3)

## 1.1 - Project Format

The basic KMOD project format is as follows:

```
KModProject ---
	KModProject.pck ---
		KModProject.gd
		(optional):
		README.txt
		docs
		etc ...
```

A DLC is like so:

```
KModProject.pck ---
		KModProject.tscn
		KModProject.gd
		(optional):
		README.txt
		docs
		etc ...
```



The reason for this is to prevent similar naming conventions (only one addon) (Bug fix)

## 1.2 - Player projects

There are very limited types of projects, However, they are ever so slowly expanding. you can use the FireOverride event:



```js
var soundGun = load("res://gunsound.wav")

var OVERRIDES = "GunFire"

func FireOverride(audioplayer: AudioStreamPlayer):
	audioplayer.stream = soundGun
	audioplayer.play() 
	audioplayer.stop()
```



Speed Overrides:

```js
var OVERRIDES = "PlayerSpeed"

var speed_enhance = true

var _speed = 3

```

Gravity Overrides:

```js
var OVERRIDES = "PhysGravity"

var gravity = 0.6
```

Jumping Power Overrides:

```js
var OVERRIDES = "JumpPower"

var jump_power = 100
```

Sliding SPEED Overrides

```js
var OVERRIDES = "SlidingSpeed"
var speed = 16
```

Sliding Event hook Overrides

```dart
func OnSlide(camera, player):
	print("Sliding!")
```

Which are checked by certain compartments of the Player (hence, why they're added into /KMOD/Player) as player scripts.

## 1.3 - KMOD SDK

From 0.0.9 on the KMOD SDK is now bundled in with your installation of KMOD, all mods are backwards compatible for now from 0.0.8, so you can use legacy mods

The KMOD SDK is the KMOD game packed into a Resource File which you can use to create mods and games with KMOD, kind of like half life.

DLCs are maps (Separate games) made in the KMOD universe.

It starts with a first person controller with guns, and variables.

Building a simple KMOD SDK DLC is like so:

```python
extends Spatial

# Called when the node enters the scene tree for the first time.


func _ready():
	# Load the SDK
	ProjectSettings.load_resource_pack("res://kmodSDK.pck")
	
	# the path of the the Player object (for FPS)
	var player = load("res://Controls/FPS.tscn").instance()
	
	# Set the player transform to the player start node
	player.global_transform.origin = $player_start.global_transform.origin
	player.allow_guns = false
	
	# add the player to the map
	add_child(player)
```

Place this as the script of your scene (named your project), then build your project by going to Export... > Windows Desktop > Zip/PCK

Copy your map.pck (or your name) to the C:/KMOD/Maps/map.pck (or your name map) Then load it in the DLC loader.

## 1.4 - DWBS "Don't Worry 'Bout Shaders"

DWBS means you wont have to worry much about post processing, mainly stuff like SSAO, AE, and more are automatically attached to the player.

![image-20220223202538777](C:\Users\seymo\AppData\Roaming\Typora\typora-user-images\image-20220223202538777.png)

(**In-game**)

![image-20220223202625200](C:\Users\seymo\AppData\Roaming\Typora\typora-user-images\image-20220223202625200.png)

It's recommended not to mess with these values due to the nature of complexity when it comes to calculating the shader validity (especially with LITE MODE)

## 1.4.1 - Exporting Maps

Exporting maps is like any other type of KMOD addon, in godot you export like a game, but instead of choosing Build Project (or Export Project) you choose Build PCK/Zip.

![image-20220223202856324](C:\Users\seymo\AppData\Roaming\Typora\typora-user-images\image-20220223202856324.png)



## 1.5 - ABI Compatibility

DLCs were introduced in 0.0.9 as a way to create games and mods using the KMOD SDK (Also introduced in 0.0.9), but what was not in mind was compatibility, the KMOD SDK is only for windows currently (With windows in mind), no other OSes are supported at this time.



Considering the versions, you need to look at the requirements (what is the DLC asking from the SDK)

usually, if the runtime DLC doesn't get the requested value from the player (whether it be shader, or any other object from the SDK)

It'll throw an error and crash, or continue.

For this, Map creators should let the user know which version of the KMOD SDK Is required as well as

- OS

- Is it bundled

  

## 1.6 - Lite Mode

KMOD was always meant to be a scalable game, to be played anywhere, at anytime.

But sometimes users don't always have high-end computers, and can't really handle the KMOD Real time shadows, therefore, workarounds are needed:

![image-20220223203515907](C:\Users\seymo\AppData\Roaming\Typora\typora-user-images\image-20220223203515907.png)

LITE MODE is essentially where it scales down the graphics from the default environment to give a higher FPS experience for KMOD, with quality as a sacarfice.

### 1.6.1 - Real-Time Shadows requirements

These are the minimum to get a 60+ FPS experience with KMOD's Real-time graphics.

- Intel i5-10300H
- RTX 2060
- 8gb RAM
- Any storage

### 1.6.2 - Optimization

Usually some maps, knowing about the Godot optimization problem, will attempt to allow a "No Shadows" or "Low Graphics" option, this is purely Map-controlled, so please look into the map's description before loosely installing maps thinking they're super optimized.

​																												( image of kmod_prototype)

![image-20220223204451841](C:\Users\seymo\AppData\Roaming\Typora\typora-user-images\image-20220223204451841.png)

## 1.7 - Player variables

Maps get the player object, essentially creating a sub-game inside the game, as a DLC, this can be anything, but considering the KMOD SDK, we're using the First person Script (FPS).

You can set different player variables in the scripts by creating an instance of the "Player" and modifying it.

Here's a list of different variables from the official "KMOD SDK".

```js
export var detached = false // is the camera detached?
export var stepup = false // is stepup allowed?
export var allow_sliding = true // Should the player be able to gain speed with CTRL
export var allow_guns = true // can the player use guns?
export var CurrentGun = 1 // what is the current gun? (see "Player Gun Slots" for more information)
export var allowFOVEffects = true // Deprecated: Being removed in version 0.1.1
```

Constants:

```js
const VERSION = "VERSION"
```

## 1.8 - Player gun slots

There's no current DLC Support for guns, but that should come in 0.1.1, where you can create custom guns, and the engine's the limit!
