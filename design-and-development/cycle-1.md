# 2.2.1 Cycle 1 - Player & Movement

## Design

### Objectives

My focus in the first cycle will be on the player and movement. My objectives are:

* [x] Load a player sprite
* [x] Be able to move the player with WASD keys
* [x] Dash can be activated to increase movement speed
* [x] Dash can only be used every 3 seconds
* [x] Dash lasts a second

### Usability Features

Movement controls are one of the key features which is required to allow users to play my game.

### Key Variables

| Variable Name | Use                                                                                                                             |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `player`      | Represents the player character in the game. It is created using the `add` function and assigned a sprite and initial position. |
| `speed`       | Represents the movement speed of the player character. It is initially set to 200.                                              |
| `cooldown`    | A boolean variable used to track whether the dash ability is on cooldown or not. It is initially set to `false`.                |

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

Pressing the WASD keys moves the player sprite in the corresponding direction by `speed` pixels per second. By holding the movement speed as a variable, I can easily change the player's speed without having to modify the entire code.

```javascript
// Set the initial movement speed
let speed = 200;


// Define the movement controls
onKeyDown("a", () => {
  player.move(-speed, 0); // Move the player character to the left (negative x-direction)
});

onKeyDown("d", () => {
  player.move(speed, 0); // Move the player character to the right (positive x-direction)
});

onKeyDown("w", () => {
  player.move(0, -speed); // Move the player character upwards (negative y-direction)
});

onKeyDown("s", () => {
  player.move(0, speed); // Move the player character downwards (positive y-direction)
});
```

Pressing the spacebar increases `speed` by 300 for half a second. The `cooldown` variable prevents this dash ability from being used again within 3 seconds.&#x20;

```javascript
// Set the dash ability cooldown flag
let cooldown = false;

// Activate the dash ability
onKeyDown("space", () => {
  if (!cooldown) {
    cooldown = true;
    speed += 300; // Increase the movement speed for dashing
    wait(0.5, () => {
      speed -= 300; // Restore the original movement speed
      wait(3, () => {
        cooldown = false; // Reset the cooldown flag after the dash duration
      });
    });
  };
});
```

### Challenges

I had planned for the dash to be activated using the right mouse button, however, I couldn't find kaboom's support for detecting right clicks. Therefore I made it activate with the spacebar instead.

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
