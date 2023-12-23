# Custom gamemodes for Dodecadone
Dodecadone has extensive modification support that allows for the quick & easy creation of custom modes, whilst being secure & fun!

Although multiple cannot be loaded at once, these are called "mod files". They are simply JavaScript files that define configuration & functions for how the game is run. They are sandboxed heavily, and cannot access the network.

The game's default gamemode, the original Dodecadone, is included. This is alongside a more advanced example, being a recreation of a Suika game, but extremely cursed.

Dodecadone's engine is p5play (https://p5play.org) & all the methods imported from the library are public, so you can use things like random() & their amazing physical properties like mass and friction. In fact, the entire level & every shape is built with sprites in this engine, so you can go nuts modifying it!

### Where do I start?
Copy the default mod file & read these docs to learn the ropes, then once you're ready to test, use the in-game mod loader. Once you're ready to publish to the Steam workshop to share with friends, use the in-game publishing guide.

By distributing anything on the Steam workshop for the game, you agree to the conditions of the Steam subscriber agreement. https://store.steampowered.com/subscriber_agreement/

You may also be contacted by me, coding398, about including your gamemode in an official form in the game. Keep an eye out in the comments for anything tagged with **[developer]**!

### How it works
A mod file loads a single object, the mod constant.
```js
// Example mod
// Your Name

const mod = {

}
```
That's literally it- you can also declare other variables outside of this, however this object is where you'll be spending most of your time.

### Top-level parameters to "mod"
- "shapes" (Object)
  - Read more later on in the docs
- "traits" (Array)
  - Read more later on in the docs
- "map" (Function)
  - Read more later on in the docs
- "runner" (Function)
  - This function will be executed inside of the initial setup() function of p5play. This signals that the game is ready, and you can do whatever the hell you want here.
- "sounds" (Object)
  - All default sounds are automatically loaded before setup, including "merge", "newBest", "ghulp", "cough", "bang", "bang2", "kick", "death", "tick", "poke", "drop", "combo", "music".
  - Every imported sound in this object either will override a default sound or add a new one to the global "sounds" object.
  - It is reccomended to import these sounds via a Base64 URL.
  - ```js
    sounds: {
      sampleSound: new Audio("data:audio/wav;base64,")
    },
    ```
- "background" (String)
  - The CSS "background" parameter for the body. For example, `radial-gradient(circle, rgba(100,100,100,1) 0%, rgba(0,0,0,0.7) 100%)` is valid.
  - For more complex backgrounds, you can override it in the runner funciton.
- "sideLeniance" (Number)
  - When a player is attempting to place a shape, the spawn bar by default goes anywhere the cursor is, but not past flipperL or flipperR. This determines how leniant the system is, so play around with it until it feels right.
  - Negatives are accepted.
- "canUseFlippers" (Number)
  - If "Allow rotating sides" should be shown.
- "noNextIs" (Boolean)
  - Enabling this disables the "Next is" text unless the nextIsGenerator is supplied.
- "nextIsGenerator" (Function)
  - If "noNextIs" is `true` and this function is supplied, the game will replace the "Next is" text with "Next is [function output]". The language localisation for the string "Next is" is handled automatically.

### The map
It's quite simple, really. When the main runner is ready, it calls the map function with a p5play `Group` (grounds) to place the map in. This is simply just a few shapes.

First, initialise some properties of the grounds:
```js
grounds.collider = 's' // Keep this! Makes it static.
grounds.color = '#080808'
grounds.stroke = "black"
```

The only thing required is a `floor`. This can be as simple as:
```js
floor = new grounds.Sprite();
floor.width = 1005;
floor.height = 15;
floor.y = 528
```

**The floor's `y` position is strongly reccomended to be kept at 528.**

#### Flippers, or "Side rotations"
For `sideLeniance` to function alongside the "Allow rotating sides" option, flippers are required to be set. This is as simple as:
```js
flipperL = sides[0]
flipperR = sides[1]
// Both of these "sides" are p5play Sprites inside of the "grounds" group.
```

### The shapes
***DUN DUN DUN***, just kidding, we aren't at the traits yet!

This is a simple object that describes the hierarchy of shapes. By default, the first shape "0" is spawned upon the spacebar being pressed, and "Dodeacdone" is achieved when the last shape is merged/spawned.

The object counts up from zero. For example,
```js
0: {
  shape: "triangle",
  color: "yellow",
  radius: 50
},
1: {
  shape: "square",
  color: "rgb(100,100,255)",
  radius: 55
},
2: {
  shape: "pentagon",
  color: "purple",
  radius: 60
},
3: {
  shape: "hexagon",
  color: "orange",
  radius: 70
},
4: {
  shape: "septagon",
  color: "cyan",
  radius: 80
},
5: {
  shape: "octagon",
  color: "#7e5ad1",
  radius: 90
},
```

for each of these, a "name" can also be supplied that overrides the shape name in the "Next is" and "Best is" text. The shape can also be provided as "circle".

### The traits
What's Dodecadone all about? The traits! Oh so much variety.

Traits can be as simple as changing one physical attribute to branches upon branches of functions.

`traits` is simply an array of all of the traits, each being an Object that can hold these values:
- "name" (String)
  - Simple name for the trait
- "localisations" (Object)
  - If this is not passed, the "name" is used by default with every language.
  - Example:
  ```js
  localisations: {
    english: "smile",
    french: "sourire"
  },
  ```
- "face" (String)
  - A base64 URL encoded face. The default smile is:
    `data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAJYAAAAyCAYAAAC+jCIaAAAAAXNSR0IArs4c6QAAAv5JREFUeF7tmzFy2zAQRT88ql3kMBl3ci6RyQ3SpXdlJjdw6xtkfIQUVspcJifwmB5QI4umKHIX4AIQ+NUKWCzePi0oinLgiwQMCDiDmAxJAqBYlMCEAMUywcqgFIsOmBCgWCZYGZRi0QETAhTLBCuDUiw6sHUOTdvi9ojC7YC2AfA3FM+axdoC8PB6QLEDNg3wEgw0tBCZ5t17Bl6CdjwBz+dnSG5rFasDOgEsGGhIETLN8R+snWBt/8FTf9DWKJYpUEGhShnyPOjWH/M6tjEv3xdt0msU6xkOtw4ObXvmANhTDAKqLUDG8ec3f3o2qj1RT8gIYqmlJ23qXcD6K4+a+bTdD3oyGmoO6glLVTdXHAe89ykB15r5yJTaF0rNQT0hlxALrmsKdME8rUN1lwSCjhV0SbBGsaYvWo/lDAJqbcOC8QVfYrqezm+FQugCoF2kIKDCHEoZZnbbZY0dyxfVDGgpxijyON4o/niLgXfeFRB7Qzdbh5emPdx530P1x18U0LBc6pu11o5VXyUL2xHFKqwgtaRDsWqpZGH7oFiFFaSWdChWLZUsbB8Uq7CC1JIOxaqlkoXtg2IVVpBa0qFYtVSysH1QrPiC+Dv1/ieiw2sNjzXPUqNYs4gmBwyl6g/OLdgDgB+9hH4D+Ba3XflsiiVnNTby5NmuwcODGeT6dA38/wPg80jCTwC+xm1ZNptiyTidGyV8aPCqAV6D/kalSG8D4BeAu5k5SWqeZBEFnEsbKhTL5vrrCrh/nf4b2xjPJDVPssil2aLId+65rpFQ3WEZe0QGrPueSpKaJ1lEUahLHBpT5NT7fQTwPcWiFGs5ytpjcbCy4D9Dcbn+A3ATF0I+m2LJWc2NLKZzjSgae/TO7f3kfYqlRjY5Ialcgh6XXKgDHYq1rFjDaElF6y2eTSiKZSvUvGCCdqNIMbtIw1zZsRTVW2CoroPNy1ecUOxYC1jCEOcJsGPRDhMCFMsEK4NSLDpgQoBimWBlUIpFB0wIUCwTrAxKseiACYE31rZpM1s/5DkAAAAASUVORK5CYII=`
- "isTrash" (Boolean)
  - If this is True, all other functions will be ignored and the shape will spawn as trash.
- "onSpawn" (Function)
  - Upon the shape's initial spawn, merge OR trait change, this function is ran with the shape as the only parameter.
  - ```js
    onSpawn: (shape)=>{
      shape.bounciness = 1
    }
    ```
- "onUpdate" (Function)
  - Called every frame where the shape is alive (shape.dead == false). The only parameter is the shape.
  - ```js
    onUpdate: (s)=>{
      s.scale += 0.001
    }
    ```
- "preMerge" (Function)
  - Called with the (shape, otherShape) parameters, before a shape is set to merge. The merge won't occur if the function does not return `true`.
  - If the function is not supplied, it is assumed the shape can merge.
  - For example, the void trait:
    ```js
    preMerge: (shape, other)=>{
        if(other.void == true) return true
        else return false;
      },
    ```
- "onTouch" (Function)
  - Called every frame the shape is touching another shape, even if they can't merge, with the (shape, otherShape) parameters.
- "useDefaultMerge" (Boolean) and "defaultMergeOverride" (Function)
  - if useDefaultMerge is `false`, `defaultMergeOverride` is called with the (shape, otherShape) parameters instead of the built-in merge function.
- "contributesToCombo" (Boolean)
  - if false & it uses the default merging function, it will not contribute to the combo.
- "isDeadTrait" (Boolean)
  - if this trait is dead. Yes, a "dead" trait is expected to be provided, read more below.
- "canBeNaturallySpawned" (Boolean)
  - if false, the shape will not appear in the queue of shapes to be dropped.
- "indexGenerator" (Function)
  - An advanced function that is called when a shape of this trait is initially spawned by the player, not merged. If supplied, this function is called to override the shape index of the shape.
  - For example, if `indexGenerator: () => 2` is supplied, every spawned shape of this trait will not spawn as the first shape in "shapes", but the 2nd (or "1" in the "shapes" object).
  - Indexes of shapes correspond to its place in the "shapes" object, however start at 1 instead of 0.

#### Reccomended traits to include
A "dead" trait is reccomended to be provided, so that a shape can easily run `shape.trait = "dead"` to die.
```js
{
  name: "dead",
  localisations: {
    english: "dead"
  },
  face: "data:image/png;base64,", // Your face here!
  canBeNaturallySpawned: false,
  isDeadTrait: true,
  preMerge: ()=>{ // Cannot merge
    return false;
  },
  onSpawn: (shape)=>{ // Shape.dead is set to true to prevent onUpdate from being called
    shape.index = -1
    shape.dead = true,
    shape.color = "rgb(100,100,100)"
  }
},
```

### A note about indexes
`shape.index` corresponds to its place in the `shapes` object, but starts at 1 instead of 0. Index 4 would correspond to shape 3, e.g.
```js
3: {
  shape: "hexagon",
  color: "orange",
  radius: 70
}
```

### Global variables to use
- `shapeG` is a group of all shapes. For example, to repulse all shapes from one, do `shapeG.attractTo(shape, -100)`. You can iterate over this.
- `floor` is the floor.
- `trash` is similar to shapeG, but for trash.
- `sounds` is the default sounds + any included in the provided `sounds` object of the mod.
- `frames` is how many frames have passed since the first shape was spawned, at a rate of 60FPS (this can slow due to lag). It also resets if all shapes die/respawn.
- `best` is the index of the best shape currently in play.

### Shape variables to use
e.g. `shape.scale = 2`

- All physical attributes described on https://p5play.org/learn/sprite.html?page=9
- `trait` is the shape's trait
- `index` is the shape's index
- `vel` is velocity (https://p5play.org/learn/sprite.html?page=3)
- `face` is another child object with the face image on it, e.g. `face.img = "https://a.b.c/"`
  - Important to note that when overriding the face's image in code, if you're using a base64 URL, make sure to also do `.replace("/", "./")`
- `stroke` is a color outline
- `scale` is the shape's scale
- `canDie` is a default variable set to `true` when a shape is first spawned, interpret as you wish
- `aliveFor` is how many frames the shape has been alive for

Also, all other shape attributes p5play provides like color, position, rotation and everything are all apart of the shape.

### Extra notes
- LocalStorage in mods is temporary. Access it with `LCL.getItem()`.
- The default mod here does not use that rule, so it breaks when under mod conditions.
- To get the SFX volume, use `Number(LCL.getItem("settingSFXVolume"))`
