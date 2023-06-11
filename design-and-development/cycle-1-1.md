# 2.2.1 Cycle 1

## Design

### Objectives

My objectives in this cycle are:

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

```
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

The project is initialised and the player sprite is loaded and added to the screen.

```typescript
import kaboom from "kaboom";
import "kaboom/global";

kaboom();

loadSprite("bean", "/sprites/bean.png");

  // adds the player
const player = add([
  sprite("bean"),
  pos(100, 200),
]);
```

Pressing WASD keys moves the player sprite in the corresponding direction by _speed_ pixels per second. By holding the movement speed as a variable, I can alter the movement speed without changing the control's code.

```typescript
// movement speed
let speed = 200;

  //movement controls
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

Pressing the spacebar increases _speed_ by 300 for half a second which makes the player sprite move faster during this time. The implementation of the _cooldown_ variable prevents this dash ability from being used again within 3 seconds.&#x20;

```typescript
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
```

### Challenges

I had planned for the dash to be activated using the right mouse button, however, I couldn't find kaboom's support for detecting right clicks. Therefore I made it activate with the spacebar instead.

```typescript
onKeyDown("space", () => {
```

But later I found how to detect right clicks so amended my code accordingly.

```typescript
onMousePress("right", () => {
```

## Testing

### Tests

| Test | Instructions                                                   | What I expect                                         | What actually happens | Pass/Fail |
| ---- | -------------------------------------------------------------- | ----------------------------------------------------- | --------------------- | --------- |
| 1    | Run code                                                       | Player sprite appears on stage                        | As expected           | Pass      |
| 2    | Move using W A S D                                             | Player moves up, left, down and right accordingly     | As expected           | Pass      |
| 3    | Press spacebar while moving                                    | Movement speed increases for a short duration         | As expected           | Pass      |
| 4    | Press spacebar multiple times in quick succession while moving | Dash only activates once until 3 seconds have elapsed | As expected           | Pass      |

### Evidence

{% embed url="https://www.youtube.com/watch?v=gfo_MYvjkGA" %}
