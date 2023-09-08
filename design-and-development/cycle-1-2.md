# 2.2.2 Cycle 2 - Aiming & Bullets

## Design

### Objectives

My focus in this cycle will be on aiming and bullets. My objectives are:

* [x] Clicking the mouse creates a bullet at the player's position
* [x] The bullet moves towards where the cursor was upon click
* [x] The bullet is orientated to point in the direction of travel

### Usability Features

### Key Variables

| Variable Name    | Use                                                                                                                                                                                                                            |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `BULLET_SPEED`   | This constant variable represents the speed of the bullet when it is spawned. It is set to a value of 1000.                                                                                                                    |
| `POINT_CURSOR`   | This variable is assigned the value of the player's position angle (in degrees) plus 180, indicating the direction in which the bullet should move. The angle is calculated using the `player.pos.angle(mousePos())` function. |
| `playerPosition` | This parameter is passed to the `spawnBullet` function and represents the position of the player. It is used to set the initial position of the bullet.                                                                        |

### Pseudocode

```
Load sprite "egg" from file path "/sprites/egg.png"
Set constant BULLET_SPEED to 1000

Function spawnBullet with parameter playerPosition
    Set POINT_CURSOR to player's position angle (in degrees) plus 180
    Create a bullet entity with the following properties:
        Set sprite to "egg"
        Add an area component for collision detection
        Set scale to 0.65, 0.65
        Set position to playerPosition
        Set color to (127, 127, 255)
        Set anchor point to "center"
        Rotate by POINT_CURSOR plus 90 degrees
        Move in the direction of POINT_CURSOR with a speed of BULLET_SPEED
        Destroy if it goes offscreen
        Tag as "player_bullet"

On mouse press
    Call spawnBullet with the player's position
```

## Development

### Outcome

```javascript
// Load the sprite named "egg" from the file path "/sprites/egg.png"
loadSprite("egg", "/sprites/egg.png");

// Set the constant variable BULLET_SPEED to 1000
const BULLET_SPEED = 1000;
```

When the `spawnBullet` function is called, it finds the bearing to the mouse cursor and then creates a bullet at the player's location. The bullet is rotated to point at the cursor position before being set to move at `BULLET_SPEED`.

```javascript
// Define the function spawnBullet with playerPosition as the parameter
function spawnBullet(playerPosition) {
    // Calculate the direction to move based on the player's position and the mouse position
    const POINT_CURSOR = player.pos.angle(mousePos()) + 180;
    
    // Create a bullet entity with the following properties
    add([
        // Set the sprite to "egg"
        sprite("egg"),
        // Add an area component for collision detection
        area(),
        // Scale the bullet sprite to 0.65 of its original size in both x and y directions
        scale(0.65, 0.65),
        // Set the position to the player's position
        pos(playerPosition),
        // Set the color of the bullet to a shade of blue
        color(127, 127, 255),
        // Set the anchor point of the bullet sprite to the center
        anchor("center"),
        // Rotate the bullet sprite to point in the direction of travel
        rotate(POINT_CURSOR + 90),
        // Move the bullet in the direction specified by POINT_CURSOR with the given speed
        move(POINT_CURSOR, BULLET_SPEED),
        // Destroy the bullet entity if it goes offscreen
        offscreen({ destroy: true }),
        // Assign the tag "player_bullet" to the bullet entity
        "player_bullet",
    ]);
}
```

Clicking the left mouse button calls the spawnBullet function and passes it the player's position.

```javascript
// Add an event listener for mouse press
onMousePress(() => spawnBullet(player.pos));
```

### Challenges

I experienced bugs where the bullet was:

* Moving in the opposite direction to the cursor
* Travelling side-on as opposed to pointing in the direction of travel

To fix this I had to add 180 degrees and 90 degrees to the angles for movement direction and rotation so the bullet moves correctly.

## Testing

### Tests

<table data-full-width="false"><thead><tr><th>Test</th><th>Instructions</th><th>What I expect</th><th>What actually happens</th><th>Pass/Fail</th></tr></thead><tbody><tr><td>1</td><td>Click mouse.</td><td><ul><li>Bullet appears in the player and moves towards mouse cursor.</li><li>Bullet is orientated correctly.</li></ul></td><td><ul><li>The bullet appears outside of the player sprite.</li><li>As expected.</li></ul></td><td>Fail.</td></tr><tr><td>2</td><td>Click mouse repeatedly.</td><td>Multiple bullets appear and behave correctly.</td><td>As expected.</td><td>Pass.</td></tr></tbody></table>

Testing was successful except the bullets appeared slightly outside the player sprite. I will aim to fix this in an upcoming cycle.

### Evidence

{% embed url="https://www.youtube.com/watch?v=OezO5c2V2AQ" %}
