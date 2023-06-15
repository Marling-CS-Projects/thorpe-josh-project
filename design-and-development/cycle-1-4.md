# 2.2.4 Cycle 4

## Design

### Objectives

My objectives in this cycle are:

* [x] Fix the bullet spawn position
* [x] Change the stage background
* [x] Increase the player movement speed
* [x] Add a check for beating the last level

### Usability Features

### Key Variables

| Variable Name | Use                                                                                |
| ------------- | ---------------------------------------------------------------------------------- |
| truePosition  | Holds the actual position of the player, calculated using player.pos and a vector. |

### Pseudocode

For pseudocode on bullet spawning, see [Cycle 2](cycle-1-2.md). Below is the updated pseudocode for level incrementation.&#x20;

<pre><code>when key "r" pressed: {
<strong>    var levelId += 1;
</strong>    destroy all;
    if (levelId > possibleLevels.length - 1) {
        go scene ('win');
    } else {
        go scene ('level', levelId);
    }
</code></pre>

## Development

### Outcome

Initially, sets the background colour and font for the project when initialising.

```typescript
kaboom({
    font: "sans-serif",
    background: [ 153, 111, 101 ],
});
```

I increased the player's movement speed by 50. Since this is stored as a variable this was simply a case of increasing a number - much easier than if I had to change each key control individually.

<pre class="language-typescript" data-full-width="false"><code class="lang-typescript"><strong>    // movement speed
</strong>    let playerSpeed = 250;
</code></pre>

I updated the bullet spawning function to receive the actual position to spawn at called _truePosition_ and to use that for calculations. This replaces the use of _playerPosition_.

```typescript
    //spawns the bullet
    function spawnBullet(truePosition) {
        //gets the direction
        const POINT_CURSOR = truePosition.angle(mousePos()) + 180;
        //adds the bullet
        add([
            pos(truePosition),               
            sprite("egg"),
            area(),
            scale(0.65,0.65),
            color(127, 127, 255),
            anchor("center"),
            rotate(POINT_CURSOR + 90),
            move(POINT_CURSOR, BULLET_SPEED),
            offscreen({ destroy: true }),
            "player_bullet",
        ])
    }
    
    //spawns a bullet on click
    onMousePress("left",() => spawnBullet(player.pos.add(vec2(350, 50))));
```

I modified the level increment code to include a check for if the last level has been reached. If it attempts to increment on the last level then it will go to the "win" scene which will be the end screen.

```typescript
    //increments levelId and goes to that level
    onKeyPress("r", () => {
        levelId += 1;
        destroyAll("entity");
        if (levelId > possibleLevels.length - 1) {
            go("win");
        } else {
            go("level", levelId);
        }
    });
```

Currently, the 'win' scene is empty so nothing happens yet when you win.

```typescript
scene("win", () => {
})
```

### Challenges

It was challenging to resolve the bullet spawn position issue since I struggled to locate what was wrong. Through testing, I found the issue to be something wrong with player.pos. To resolve the issue I experimented with adding a vector onto player.pos to create _truePosition_ to make it correct. I'm not sure what's wrong with player.pos but it could be something to do with how i define player from the level generation.

## Testing

### Tests

| Test | Instructions                                   | What I expect                                                                                                 | What actually happens | Pass/Fail |
| ---- | ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------- | --------------------- | --------- |
| 1    | Run code                                       | Background is the correct colour ([this](https://color-hex.org/color/996f65))                                 | As expected           | Pass      |
| 2    | Move around with WASD and dash                 | Player moves faster than in [Cycle 3](cycle-1-3.md)                                                           | As expected           | Pass      |
| 3    | Repeated presses of 'r' key to increment level | Game switches to the next level each time and after the last level goes to a blank background (the end scene) | As expected           | Pass      |
| 4    | Click mouse repeatedly                         | Bullets appear in the player and move towards mouse cursor until going offscreen                              | As expected           | Pass      |

### Images

<figure><img src="../.gitbook/assets/cycle4backgroundcolour.png" alt="" width="375"><figcaption><p>Background colour</p></figcaption></figure>

### Evidence

{% embed url="https://youtu.be/1pqgNkmAqj4" %}
