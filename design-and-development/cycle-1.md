# 2.2.1 Cycle 1 - Player & Movement

## Design

### Objectives

My focus in the first cycle will be on adding the player and movement controls. My objectives are:

* [x] Load a player sprite
* [x] Be able to move the player with WASD keys
* [x] Dash can be activated to increase movement speed
* [x] Dash can only be used every 3 seconds
* [x] Movement speed increase lasts for 1 second

### Usability Features

Movement controls are needed to allow the player to move around the game scene and be able to play my game.

### Key Variables

| Variable Name | Use                                                                                                                               |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `player`      | Represents the player character in the game. It is created using the `add` function and assigned a sprite and initial position.   |
| `speed`       | Represents the movement speed of the player character. It is initially set to 200 and later increased when the dash is activated. |
| `cooldown`    | A boolean variable used to track whether the dash ability is on cooldown or not. It is initially set to `false`.                  |

### Pseudocode

```
import kaboom from "kaboom";
import "kaboom/global";

kaboom();

// Load the sprite for the player character
loadSprite("bean", "/sprites/bean.png");

// Create the player character
const player = add([
  sprite("bean"),
  pos(100, 200),
]);

// Set the initial movement speed
let speed = 200;

// Set the dash ability cooldown flag
let cooldown = false;

// Define the movement controls
onKeyDown("a", () => {
  player.move(-speed, 0);
});

onKeyDown("d", () => {
  player.move(speed, 0);
});

onKeyDown("w", () => {
  player.move(0, -speed);
});

onKeyDown("s", () => {
  player.move(0, speed);
});

// Activate the dash ability
onKeyDown("space", () => {
  if (!cooldown) {
    cooldown = true;
    speed += 300; // Increase the movement speed
    wait(0.5, () => {
      speed -= 300; // Restore the original movement speed
      wait(3, () => {
        cooldown = false; // Reset the cooldown flag
      });
    });
  }
});
```

## Development

### Outcome

The project is initialised and the player sprite is loaded and added to the screen.

```javascript
import kaboom from "kaboom";
import "kaboom/global";

kaboom();

loadSprite("bean", "/sprites/bean.png");

// Create the player character
const player = add([
  sprite("bean"), // Assign it the "bean" sprite
  pos(100, 200), // Set the initial position
]);
```

Pressing the WASD keys moves the player sprite in the corresponding direction by `speed` pixels per second. By storing the movement speed as a variable, I can easily change the player's speed in the future.

```javascript
// Set the initial movement speed
let speed = 200;

// Define the movement controls
onKeyDown("a", () => {
  player.move(-speed, 0); // Move left
});

onKeyDown("d", () => {
  player.move(speed, 0); // Move right
});

onKeyDown("w", () => {
  player.move(0, -speed); // Move up
});

onKeyDown("s", () => {
  player.move(0, speed); // Move down
});
```

Pressing the spacebar increases `speed` by 300 for half a second. The `cooldown` variable prevents this dash ability from being used again within 3 seconds.&#x20;

```javascript
// Set the dash ability cooldown flag
let cooldown = false;

// Activate the dash ability when the spacebar is pressed
onKeyDown("space", () => {
  if (!cooldown) {
    cooldown = true;
    speed += 300; // Increase movement speed for dashing
    wait(0.5, () => {
      speed -= 300; // Restore original movement speed
      wait(3, () => {
        cooldown = false; // Reset the cooldown flag after dash has ended
      });
    });
  };
});
```

### Challenges

I wanted to activate the dash using the right mouse button but I couldn't find how to detect right clicks with Kaboom. Therefore I made it activate with the spacebar instead.

```typescript
onKeyDown("space", () => {
```

But later I found how to detect right clicks so modified my code accordingly.

```typescript
onMousePress("right", () => {
```

## Testing

### Tests

| Test | Instructions                                                    | What I expect                                             | What actually happens | Pass/Fail |
| ---- | --------------------------------------------------------------- | --------------------------------------------------------- | --------------------- | --------- |
| 1    | Run code.                                                       | Player sprite appears on stage.                           | As expected.          | Pass.     |
| 2    | Move using WASD keys.                                           | Player moves in the correct direction for each key press. | As expected.          | Pass.     |
| 3    | Press spacebar while moving.                                    | Movement speed increases for a short duration.            | As expected.          | Pass.     |
| 4    | Press spacebar multiple times in quick succession while moving. | Dash only activates once every 3 seconds.                 | As expected.          | Pass.     |

### Evidence

{% embed url="https://www.youtube.com/watch?v=gfo_MYvjkGA" %}
