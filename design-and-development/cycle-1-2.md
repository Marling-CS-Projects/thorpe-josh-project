# 2.2.3 Cycle 3 - Room Generation & Collisions

## Design

### Objectives

My focus in this cycle is on room generation. My objectives are:

* [x] Create rooms in a grid system
* [x] Rooms consist of walls, spikes, and boxes
* [x] Every room is stored in an array so that the next one can be added when the last is defeated
* [x] Press t to start playing rooms
* [x] Press r to move on to the next room
* [x] Add player collision for walls and boxes so the player can not walk through

### Usability Features

The tiles in the level use contrasting colours so that players can more easily tell them apart. This also makes my game more accessible for users with visual impairment.

### Key Variables

| Variable Name    | Use                                                                                                                                                                |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `possibleLevels` | This array is imported from the `./possibleLevels` file and contains level data for the game.                                                                      |
| `levelId`        | This variable represents the current level identifier.                                                                                                             |
| `level`          | This is the level object created using `addLevel()` function and contains the tile map for the current level. It is used to define the tiles and their properties. |

### Pseudocode

```
Import "possibleLevels" from "./possibleLevels"

Load sprite resources:
    loadSprite("door", "/sprites/door.png")
    loadSprite("grass", "/sprites/grass.png")
    loadSprite("spike", "/sprites/spike.png")
    loadSprite("mushroom", "/sprites/mushroom.png")

Define game start event:
    onKeyPress("t", () => {
        levelId = 0
        goToLevel(levelId)
    })

Define scene for each level:
    scene("level", (levelId) => {
        level = addLevel(possibleLevels[levelId], {
            tileWidth: 58,
            tileHeight: 58,
            pos: vec2(350, 45),
            tiles: {
                "=": () => [
                    sprite("grass"),
                    area(),
                    anchor("center"),
                    body({ isStatic: true }),
                    "entity",
                    "wall",
                ],
                "^": () => [
                    sprite("spike"),
                    area(),
                    anchor("center"),
                    "entity",
                    "spike",
                ],
                "+": () => [
                    sprite("mushroom"),
                    area(),
                    anchor("center"),
                    body({ isStatic: true }),
                    "entity",
                    "box",
                ],
                "8": () => [
                    sprite("door"),
                    area(),
                    anchor("center"),
                    body({ isStatic: true }),
                    "entity",
                    "door",
                ],
                "@": () => [
                    sprite("bean"),
                    area(),
                    anchor("center"),
                    body(),
                    "player",
                ],
            }
        })

        Define event to advance to the next level:
        onKeyPress("r", () => {
            levelId += 1
            destroyAll("entity")
            goToLevel(levelId)
        })

        Obtain player entity reference from the level:
        player = level.get("player")[0]

        // Movement and bullet code goes here

    })

Define function to go to a specific level:
    function goToLevel(levelId):
        destroyAll()
        go("level", levelId)

Start the game:
    init(() => {
        // Additional game initialization code
    })
```

## Development

### Outcome

The `possibleLevels` array is imported from another file and the necessary sprites are loaded.

{% code title="main.js" %}
```javascript
import { possibleLevels } from "./possibleLevels";

loadSprite("door", "/sprites/door.png");
loadSprite("grass", "/sprites/grass.png");
loadSprite("spike", "/sprites/spike.png");
loadSprite("mushroom", "/sprites/mushroom.png");
```
{% endcode %}

The `possibleLevels` array holds each level in symbol form which will then be translated into stationary sprites to form the level. It is called `possibleLevels` because I intend to modify the generation later to be randomly selected.

{% code title="possibleLevels.js" %}
```javascript
export const possibleLevels = [
    [
    "====================",
    "=                  =",
    "=                  =",
    "=            +++   =",
    "=            + +   =",
    "=   ^  ++          =",
    "=              +   =",
    "=                  =",
    "=@    ^        ^   =",
    "=          ^   ^   =",
    "=                  =",
    "=                  =",
    "=                  =",
    "=   ^              =",
    "=                  =",
    "====================",
    ],
    [
    "====================",
    "=                  =",
    "=                  =",
    "=            + +   =",
    "=            + +   =",
    "=   ^              =",
    "=              +   =",
    "=                  =",
    "= @   ^        ^   =",
    "=          ^       =",
    "=                  =",
    "=                  =",
    "=                  =",
    "=   ^              =",
    "=                  =",
    "====================",
    ],
    ...
    // Rest of the levels
    ];
```
{% endcode %}

Pressing 't' starts the level scene with a `levelId` of 0. I'm using scenes to organise the project because it makes my code easier to understand.

{% code title="main.js" %}
```javascript
// Start the game when "t" key is pressed
onKeyPress("t", () => {
    let levelId = 0;
    go("level", levelId); // Go to the first level
});
```
{% endcode %}

The _x_th level from the `possibleLevels` array is added to the screen. Each character in the symbolic representation of the level is translated to a tile placed at that location, for example `=` corresponds to a grass block/wall. This creates the level on the screen.

{% code title="main.js" %}
```javascript
// Define the scene for each level
scene("level", (levelId) => {

    // Create the level object using the level data from possibleLevels
    const level = addLevel(possibleLevels[levelId], {
        tileWidth: 58, // Define the width and height of each tile block
        tileHeight: 58,
        pos: vec2(350, 45),
        tiles: {
            "=": () => [ // Setup walls
                sprite("grass"),
                area(),
                anchor("center"),
                body({ isStatic: true }), // Make the sprite solid and impassable
                "entity",
                "wall",
            ],
            "^": () => [ // Setup spikes
                sprite("spike"),
                area(),
                anchor("center"),
                "entity",
                "spike",
            ],
            "+": () => [ // Setup boxes
                sprite("mushroom"),
                area(),
                anchor("center"),
                body({ isStatic: true }),
                "entity",
                "box",
            ],
            "8": () => [ // Setup doors
                sprite("door"),
                area(),
                anchor("center"),
                body({ isStatic: true }),
                "entity",
                "door",
            ],
            "@": () => [ // Setup player
                sprite("bean"),
                area(),
                anchor("center"),
                body(),
                "player",
            ],
        };
    });
```
{% endcode %}

Pressing r will restart the scene with the next level from `possibleLevels`.

{% code title="main.js" %}
```javascript
    // "r" key to advance the level
    onKeyPress("r", () => {
        levelId += 1; // Increment the level identifier
        destroyAll("entity"); // Destroy all entities
        go("level", levelId); // Go to next level
    });
```
{% endcode %}

The player sprite is added to the game as part of the tile map and then a reference to it is obtained so that I can call actions using its position. Adding the player as part of the tile map means that it is much easier to change the player's spawn location in the level.

{% code title="main.js" %}
```javascript
    // Obtain a reference to the player entity
    const player = level.get("player")[0];
```
{% endcode %}

I moved the player movement and bullet code inside a scene so that these inputs have no effect until the scene has been activated (levels have begun). This will be useful for later when I create a main menu at the start of the game.

{% code title="main.js" %}
```javascript
    // ... Movement and bullet code goes here

});
```
{% endcode %}

### Challenges

While adjusting the tile dimensions and the position of the level on the screen, the player's starting position was sometimes outside the level. To simplify things, I made the player's starting point part of the tilemap for levels so that it is always easy to control where the player starts. This will also make it easier in the future when I replace the test levels with some proper levels.

<figure><img src="../.gitbook/assets/cycle3playerstart.png" alt=""><figcaption><p>The player sprites starts as part of the level</p></figcaption></figure>

## Testing

### Tests

| Test | Instructions                        | What I expect                                                       | What actually happens                                                     | Pass/Fail |
| ---- | ----------------------------------- | ------------------------------------------------------------------- | ------------------------------------------------------------------------- | --------- |
| 1    | Press the T key.                    | Level and player appears.                                           | As expected.                                                              | Pass.     |
| 2    | Repeatedly press the R key.         | Game switches to the next level each time.                          | As expected however trying to go past the last level results in an error. | Pass.     |
| 3    | Move the player with WASD and dash. | Functions the same as in [Cycle 1](cycle-1.md) & [2](cycle-1-1.md). | As expected.                                                              | Pass.     |
| 4    | Shoot with mouse click.             | Functions the same as in [Cycle 2](cycle-1-1.md).                   | Bullet spawns far away from the player.                                   | Fail.     |

Unexpectedly the bullet appears far from the player sprite when the mouse is clicked, much further compared to in [Cycle 2](cycle-1-1.md). This is a major bug which I will address in the next cycle ([Cycle 4](cycle-1-3.md)).

Testing also revealed that trying to go past the last level resulted in an error as the code tried to pull from a position in `possibleLevels` which did not exist. I will also implement a catch for this in the next cycle.

### Images

<div data-full-width="false">

<figure><img src="../.gitbook/assets/cycle3error.png" alt="" width="563"><figcaption><p>Error shown when pressing r on the last level</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/cycle3level.png" alt="" width="375"><figcaption><p>Level successfully added</p></figcaption></figure>

</div>

### Evidence

{% embed url="https://www.youtube.com/watch?v=jB5KBBgaLPU" %}
