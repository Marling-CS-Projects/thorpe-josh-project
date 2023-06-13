# 2.2.2 Cycle 2

## Design

### Objectives

My objectives in this cycle are:

* [x] &#x20;Clicking the mouse creates a bullet at the player's position
* [x] The bullet moves towards where the cursor was upon click
* [x] The bullet is orientated to point in the direction of travel

### Usability Features

### Key Variables

| Variable Name | Use                                                  |
| ------------- | ---------------------------------------------------- |
| BULLET\_SPEED | Holds the set bullet movement speed                  |
| POINT\_CURSOR | Angle which orientates from the player to the cursor |

### Pseudocode

<pre><code><strong>const BULLET_SPEED = 1000
</strong>procedure spawn bullet
    let player_position = get(player_position);
    const DIRECTION = get(angle between player and cursor);
        add bullet {
            position = player_position,
            rotate(direction),
            move(BULLET_SPEED, direction),
        };
    
end procedure

loop
    if mouse clicked: {
        spawnbullet()
    }
end loop
</code></pre>

## Development

### Outcome

The bullet sprite is loaded and the movement speed is set.

```typescript
loadSprite("egg", "/sprites/egg.png");
const BULLET_SPEED = 1000
```

When the _spawnBullet_ function is called, it finds the bearing to the mouse cursor and then creates a bullet at the player's location. The bullet is rotated to point at the cursor position before being set to move at the predetermined speed. Once it passes offscreen it is deleted.

```typescript
function spawnBullet(playerPosition) {
      //gets the direction to move
    const POINT_CURSOR = player.pos.angle(mousePos()) + 180;
    //adds the bullet
    add([
        sprite("egg"),
        area(),
        scale(0.65,0.65),
        pos(playerPosition),
        color(127, 127, 255),
        anchor("center"),
        //point the sprite in the direction of travel
        rotate(POINT_CURSOR + 90),
        //moves in the direction with speed
        move(POINT_CURSOR, BULLET_SPEED),
        offscreen({ destroy: true }),
        "player_bullet",
    ])
}
```

Clicking the left mouse button calls the spawnBullet function and passes it the player's position.

```typescript
onMousePress(() => spawnBullet(player.pos));
```

### Challenges

I experienced a bug where the bullet was:

* Moving in the opposite direction to the cursor
* Travelling side-on as opposed to pointing in the direction of travel

To fix this I had to add 180 degrees and 90 degrees to the angles for movement direction and rotation so the bullet moves correctly.

## Testing

### Tests

<table data-full-width="false"><thead><tr><th>Test</th><th>Instructions</th><th>What I expect</th><th>What actually happens</th><th>Pass/Fail</th></tr></thead><tbody><tr><td>1</td><td>Click mouse</td><td>Bullet appears in the player and moves towards mouse cursor</td><td>As expected except the bullet appears outside of the sprite</td><td>Fail</td></tr><tr><td>2</td><td>Click mouse repeatedly</td><td>Multiple bullets appear</td><td>As expected</td><td>Pass</td></tr></tbody></table>

Testing was successful except the bullets appeared slightly outside the player sprite. I will aim to fix this in an upcoming cycle.

### Evidence

{% embed url="https://www.youtube.com/watch?v=OezO5c2V2AQ" %}
