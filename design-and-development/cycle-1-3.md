# 2.2.3 Cycle 3

## Design

### Objectives

My objectives in this cycle are:

* [x] Create rooms in a grid system
* [x] Made of walls, spikes and boxes
* [x] Press t to start playing rooms
* [x] Press r to move on to the next room through the rooms
* [x] Add collision for walls and boxes

### Usability Features

### Key Variables

| Variable Name | Use                                                           |
| ------------- | ------------------------------------------------------------- |
| levelId       | Holds the position of the level to be selected from the array |
| level         | Stores the level added on screen                              |
| player        | Assigned to the player sprite in the level                    |

### Pseudocode

<pre><code><strong>import levelsList
</strong>
when key "t" pressed: {
    var levelId = 0;
    go scene ('level', levelId);
}

scene 'level' [levelId]: {
    level = addLevel (levelsList[levelId]): {
        tiles: {[
            "=" =>
            sprite: grass,
            solid,
            anchor: centre,
            "wall",
            ]},
            {[
            sprite: spike,
            anchor: centre,
            "spike",
            ]},
            {[
            sprite: mushroom,
            anchor: centre,
            solid,
            "box",
            ]},
            {[
            sprite: bean,
            anchor: centre,
            "player",
            ]},
        ]}
    }
    
    when key "r" pressed: {
    var levelId += 1;
    destroy all;
    go scene ('level', levelId);
}
player = level.get(player);

...
movement code

}
end scene
</code></pre>

## Development

### Outcome

The possibleLevels array is imported from another file and the necessary sprites are loaded.

{% code title="main.ts" %}
```typescript
import { possibleLevels } from "./possibleLevels";

loadSprite("door", "/sprites/door.png");
loadSprite("grass", "/sprites/grass.png");
loadSprite("spike", "/sprites/spike.png");
loadSprite("mushroom", "/sprites/mushroom.png");
```
{% endcode %}

_possibleLevels_ holds each level in symbol form which will then be translated into stationary sprites to form the level. It is called _possibleLevels_ because I intend to change this later to be randomly selected rooms.

{% code title="possibleLevels.ts" %}
```typescript
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
    //rest of the levels
    ]
```
{% endcode %}

Pressing t starts the level scene with _levelId_ 0. I'm using scenes since it makes it easier to organise the project and understand what is going on.

{% code title="main.ts" %}
```typescript
//starts levels
onKeyPress("t", () => {
    let levelId = 0
    go("level", levelId);
});
```
{% endcode %}

The xth level from the _possibleLevels_ array is added to the screen. Each character in the symbol representation of the level is translated to a tile which is then placed at that location, for example '=' corresponds to a grass block/wall. This creates the level on screen.

<figure><img src="../.gitbook/assets/image.png" alt="" width="375"><figcaption></figcaption></figure>

{% code title="main.ts" %}
```typescript
scene("level", (levelId) => {

    const level = addLevel(possibleLevels[levelId], {
        // define the size of tile block
        tileWidth: 58,
        tileHeight: 58,
        pos: vec2(350, 45),
        // define what each symbol means
        tiles: {
            "=": () => [
                sprite("grass"),
                area(),
                anchor("center"),
                //makes the sprite solid and impassible
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
    });
```
{% endcode %}

Pressing r will restart the scene with the next level from _possibleLevels_.

{% code title="main.ts" %}
```typescript
    //increments levelId and goes to that level
    onKeyPress("r", () => {
        levelId += 1;
        destroyAll("entity");
        go("level", levelId);
    });
```
{% endcode %}

Defines the player from the player sprite which is placed when the level is added. This new system makes it easier to change the location the player appears for each level.

{% code title="main.ts" %}
```typescript
    const player = level.get("player")[0]
```
{% endcode %}

I moved the player movement and bullet code inside the scene so that it does not detect key presses until the scene has been activated.

```typescript
    ...   
}
```

### Challenges

While adjusting the tile dimensions and the position of the level on the screen, the player's starting position became annoying as sometimes it would be outside the level each time I adjusted it. So I made the player's starting point part of the level so that it is always easy to control where it starts in the level. This will also make it easier in the future when I create some proper levels.

<figure><img src="../.gitbook/assets/Screenshot 2023-06-11 010224 (2).png" alt=""><figcaption><p>The player sprites starts as part of the level</p></figcaption></figure>

## Testing

### Tests

| Test | Instructions                       | What I expect                                                        | What actually happens                                                    | Pass/Fail |
| ---- | ---------------------------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ | --------- |
| 1    | Press 't' key                      | Level and player appears                                             | As expected                                                              | Pass      |
| 2    | Repeated presses of 'r' key        | Game switches to the next level each time                            | As expected however trying to go past the last level results in an error | Pass      |
| 3    | Move the player with WASD and dash | Functions the same as in [Cycle 1](cycle-1-1.md) & [2](cycle-1-2.md) | As expected                                                              | Pass      |
| 4    | Shoot with mouse click             | Functions the same as in [Cycle 2](cycle-1-2.md)                     | Bullet spawns far away from the player                                   | Fail      |

Unexpectedly the bullet appears far from the player sprite when the mouse is clicked, much further compared to in [Cycle 2](cycle-1-2.md). This is an important bug which I will address in the next cycle.

Testing also revealed that trying to go past the last level resulted in an error, so a catch for this will need to be implemented in an upcoming cycle.

<figure><img src="../.gitbook/assets/Screenshot 2023-06-11 013007.png" alt="" width="375"><figcaption><p>Error shown when pressing r on the last level</p></figcaption></figure>

### Evidence

{% embed url="https://www.youtube.com/watch?v=jB5KBBgaLPU" %}
