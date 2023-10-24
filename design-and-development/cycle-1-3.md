# 2.2.4 Cycle 4 - Bug Fixes & Changes

## Design

### Objectives

In this cycle, I want to fix some issues in my game and make some small changes. My objectives are:

* [x] Fix the bullet spawn position being outside the player
* [x] Change the stage background colour
* [x] Increase the player movement speed
* [x] Add a check for beating the last level to prevent errors

### Key Variables

| Variable Name  | Use                                                                                                               |
| -------------- | ----------------------------------------------------------------------------------------------------------------- |
| `truePosition` | The calculated position bullets should spawn at, calculated by taking `playerPosition` and adding a vector to it. |

### Pseudocode

For pseudocode on bullet spawning, see [Cycle 2](cycle-1-1.md). Below is the updated pseudocode for level incrementation.&#x20;

```
On "r" key press
    Increment the levelId by 1
    Destroy all entities with the "entity" tag
    If levelId is greater than the number of possibleLevels minus 1
        Go to the "win" scene
    Else
        Go to the "level" scene with the updated levelId
```

## Development

### Outcome

```javascript
kaboom({
    font: "sans-serif", // Set game font to "sans-serif"
    background: [153, 111, 101], // Set game background color to [153, 111, 101]
});
```

<pre class="language-javascript" data-full-width="false"><code class="lang-javascript"><strong>    // Movement speed
</strong>    let playerSpeed = 250;
</code></pre>

I updated the bullet spawning function to receive the actual position to spawn at called `truePosition` and to use that for calculations. This replaces the use of `playerPosition`.

```javascript
function spawnBullet(truePosition) {
    // Calculate the direction to move based on the player and mouse position
    const POINT_CURSOR = truePosition.angle(mousePos()) + 180; // Angle adjusted by 180 degrees

    add([
        pos(truePosition), // Set the position of the bullet to truePosition
        sprite("egg"),
        area(),
        scale(0.65, 0.65),
        color(127, 127, 255),
        anchor("center"),
        // Rotate the bullet sprite to point in the direction of travel
        rotate(POINT_CURSOR + 90),
        // Move the bullet in the direction specified by POINT_CURSOR with the given speed
        move(POINT_CURSOR, BULLET_SPEED),
        // Destroy the bullet entity if it goes offscreen
        offscreen({ destroy: true }),
        "player_bullet", // Adding tag
    ]);
};
```

I modified the level increment code to include a check for if the last level has been reached. If it attempts to increment on the last level then it will go to the "win" scene, which will be the end screen.

```javascript
// Increments levelId and goes to that level
onKeyPress("r", () => {
    levelId += 1;
    destroyAll("entity"); // Destroy all entities
    if (levelId > possibleLevels.length - 1) {
        go("win"); // Go to the "win" scene if levelId exceeds the number of possibleLevels
    } else {
        go("level", levelId); // Go to the "level" scene with the updated levelId
    };
});
```

Currently, the 'win' scene is empty so nothing happens yet when you win.

```javascript
// Defines the "win" scene
scene("win", () => {
    // Scene logic for the "win" scene can be added here
});
```

### Challenges

It was challenging resolving the bullet spawn position issue since I struggled to locate what was wrong. Through debugging, I found the issue to be something wrong with the use of `player.pos`. To resolve the issue I experimented with adding a vector onto `player.pos` to create `truePosition`. I fine-tuned this vector until it resulted in the bullets appearing perfectly in the player.

## Testing

### Tests

| Test | Instructions                               | What I expect                                                                                                  | What actually happens | Pass/Fail |
| ---- | ------------------------------------------ | -------------------------------------------------------------------------------------------------------------- | --------------------- | --------- |
| 1    | Run code.                                  | Background is the correct colour ([this](https://color-hex.org/color/996f65)).                                 | As expected.          | Pass.     |
| 2    | Move around with WASD and dash.            | Player moves faster than in [Cycle 3](cycle-1-2.md).                                                           | As expected.          | Pass.     |
| 3    | Repeatedly press r to increment the level. | Game switches to the next level each time and after the last level goes to a blank background (the end scene). | As expected.          | Pass.     |
| 4    | Click mouse repeatedly.                    | Bullets appear in the player and move towards mouse cursor until going offscreen.                              | As expected.          | Pass.     |

### Images

<figure><img src="../.gitbook/assets/cycle4backgroundcolour.png" alt="" width="375"><figcaption><p>Correct background colour</p></figcaption></figure>

### Evidence

{% embed url="https://youtu.be/1pqgNkmAqj4" %}
