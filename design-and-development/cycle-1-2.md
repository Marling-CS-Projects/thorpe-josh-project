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

```
const BULLET_SPEED = 1000
procedure spawn bullet
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
```

## Development

### Outcome

```typescript
const BULLET_SPEED = 1000
function spawnBullet(playerPosition) {
      //gets the direction
    const POINT_CURSOR = player.pos.angle(mousePos()) + 180;
    //adds the bullet
    add([
        sprite("egg"),
        area(),
        scale(0.65,0.65),
        pos(playerPosition),
        color(127, 127, 255),
        anchor("center"),
        move(POINT_CURSOR, BULLET_SPEED),
        rotate(POINT_CURSOR + 90),
        offscreen({ destroy: true }),
        "player_bullet",
    ])
}

onMousePress(() => spawnBullet(player.pos));
```

### Challenges

I experienced a bug where the bullet was:

* Moving in the opposite direction to the cursor
* Travelling side-on as opposed to pointing in the direction of travel

To fix this I had to add 180 degrees and 90 degrees to the angles for movement direction and rotation accordingly so now it moves correctly.

## Testing

### Tests

<table data-full-width="false"><thead><tr><th>Test</th><th>Instructions</th><th>What I expect</th><th>What actually happens</th><th>Pass/Fail</th></tr></thead><tbody><tr><td>1</td><td>Click mouse</td><td>Bullet appears in the player and moves towards mouse cursor</td><td>As expected except the bullet appears outside of the sprite</td><td>Fail</td></tr><tr><td>2</td><td>Click mouse repeatedly</td><td>Multiple bullets appear</td><td>As expected</td><td>Pass</td></tr></tbody></table>

Testing was successful except the bullet appears slightly outside the player sprite. This isn't important right now because the current player sprite is temporary but I will fix this in a coming cycle.

### Evidence

{% file src="../.gitbook/assets/cycle 2 evidence.mp4" %}
