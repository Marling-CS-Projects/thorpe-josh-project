# 2.2.1 Cycle 1

## Design

### Objectives

My objectives in this cycle are to:

* [x] Load a player sprite
* [x] Be able to move the player with WASD keys
* [x] Dash to momentarily increase speed for a second
* [x] Dash can only be used every 3 seconds
* [x] Dash lasts a second

### Usability Features

### Key Variables

| Variable Name | Use                                              |
| ------------- | ------------------------------------------------ |
| speed         | Holds the player movement speed                  |
| cooldown      | Holds whether the dash cooldown is active or not |

### Pseudocode

```javascript
import kaboom

load(player)
place(player)

if W key pressed: { 
  move player up by speed
};
if A key pressed: {
  move player left by speed
};
if S key pressed: {
  move player down by speed
};
if D key pressed: {
  move player right by speed
};

let speed = 200
cooldown = false;

if space key pressed: {
  if  cooldown = false: {
    cooldown = true;
    speed = speed + 300;
    wait 1 second;
    speed = speed - 300;
    cooldown = false;
  }
}
```

## Development

### Outcome

{% code title="main.ts" %}
```typescript
import kaboom from "kaboom";
import "kaboom/global";

kaboom();

loadSprite("bean", "/sprites/bean.png");

// movement speed
let speed = 200;

// adds the player
const player = add([
  sprite("bean"),
  pos(100, 200),
]);

// dash
let cooldown = false;
onKeyDown("space", () => {
  if (!cooldown) {
    cooldown = true;
      //speed increase
    speed += 300;
    //dash duration
    wait(0.5, () => {
        speed -= 300
        wait(3, () => {
            cooldown = false;
        });
  });
  }
});

onKeyDown("a", () => {
  // .move() moves by pixels per second in x and y
  player.move(-speed, 0);
});

onKeyDown("d", () => {
  player.move(speed, 0);main.type
});

onKeyDown("w", () => {
  player.move(0, -speed);
});

onKeyDown("s", () => {
  player.move(0, speed);
});
```
{% endcode %}

### Challenges

Initially, I planned for the dash to be activated using the right mouse button, however, kaboom doesn't have support for detecting right clicks. Therefore I've made it activate with the spacebar instead.

I had a bug where holding the spacebar down would stack the dash, causing the movement speed to be much higher than expected.

## Testing

### Tests

| Test | Instructions                                                   | What I expect                                         | What actually happens | Pass/Fail |
| ---- | -------------------------------------------------------------- | ----------------------------------------------------- | --------------------- | --------- |
| 1    | Run code                                                       | Player sprite appears on stage                        | As expected           | Pass      |
| 2    | Move using W A S D                                             | Player moves up, left, down and right accordingly     | As expected           | Pass      |
| 3    | Press spacebar while moving                                    | Movement speed increases for a short duration         | As expected           | Pass      |
| 4    | Press spacebar multiple times in quick succession while moving | Dash only activates once until 3 seconds have elapsed | As expected           | Pass      |

### Evidence

add youtube video of testing
