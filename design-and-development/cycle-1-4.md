# 2.2.5 Cycle 5 - Health Bar & Player Death

## Design

### Objectives

My focus in this cycle is to add a health bar which shows the player's current health and when they take damage. My objectives are:

* [x] Add the player health bar
* [x] Standing on spikes deals damage to the player
* [x] Taking damage will lower the health bar
* [x] Reaching 0 health causes the player to die

#### Smaller Changes

* [x] Set the player sprite's layer above the spike's layer

### Usability Features

Adding a health bar allows players to make better-informed decisions within my game for example by playing more cautiously when they can see that they have low health.

### Key Variables

| Variable Name     | Use                                                                                                                                                                             |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `playerHP`        | Represents the current hit points of the player. It is initially set to 100 and gets updated when the player gets hurt.                                                         |
| `ORIGINALHP`      | Represents the starting hit points of the player. It is set to the initial value of `playerHP` and is used to calculate the health bar width.                                   |
| `HEALTHBARWIDTH`  | Represents the width of the health bar in pixels.                                                                                                                               |
| `HEALTHBARHEIGHT` | Represents the height of the health bar in pixels.                                                                                                                              |
| `healthBarBorder` | Represents the health bar's border entity. It is a rectangle with a width and height slightly larger than the health bar itself.                                                |
| `healthBarBg`     | Represents the background of the health bar. It is a rectangle that provides the visual backdrop for the health bar.                                                            |
| `healthBar`       | Represents the actual health bar entity. It is a rectangle whose width represents the player's current health.                                                                  |
| `spikeCooldown`   | Represents a boolean flag indicating whether the player is currently in a cooldown state after colliding with a spike. It prevents the player from getting hurt too frequently. |

### Pseudocode

```
// Initialize player's hit points and starting point
let playerHP = 100
const ORIGINALHP = playerHP

// Define health bar dimensions
const HEALTHBARWIDTH = 100
const HEALTHBARHEIGHT = 20

// Create health bar entities
const healthBarBorder = createHealthBarBorder()
const healthBarBg = createHealthBarBackground()
const healthBar = createHealthBar()

// Define spike cooldown flag
let spikeCooldown = false

// Detect player-spike collisions
onCollide("player", "spike", () => {
    if (!spikeCooldown) {
        spikeCooldown = true
        player.hurt(5)
        playerHP = player.hp()
        updateHealthBar()
        wait(0.5, () => {
            spikeCooldown = false
        })
    }
})

// Handle player death
player.on("death", () => {
    destroy(player)
    wait(0.3, () => {
        go("lose")
    })
})

// Update health bar width based on player's hit points
function updateHealthBar() {
    const newWidth = (playerHP / ORIGINALHP) * HEALTHBARWIDTH
    healthBar.width = newWidth
}

// Scene for when the player loses
scene("lose", () => {
    // Handle lose scene logic
})

// Function to create health bar border entity
function createHealthBarBorder() {
    // Create and return health bar border entity
}

// Function to create health bar background entity
function createHealthBarBackground() {
    // Create and return health bar background entity
}

// Function to create health bar entity
function createHealthBar() {
    // Create and return health bar entity
}
```

## Development

### Outcome

I set the layer of the player above the layer of the spikes using the `z()` property.

<pre class="language-javascript"><code class="lang-javascript"><strong>...
</strong><strong>
</strong><strong>"^": () => [
</strong>sprite("spike"),
area(),
anchor("center"),
z(0), // Layer 0
"entity",
"spike",
],

...

"@": () => [
sprite("bean"),
area(),
anchor("center"),
z(1), // Layer 1
body(),
health(playerHP),
"player",
],
</code></pre>

I placed the following code outside of the level scene so that `playerHP` is not reset to 100 at the start of each level. Also, it would be a computational waste to repeatedly define constants.

```javascript
// Sets the player hitpoints and saves the starting point
let playerHP = 100;
const ORIGINALHP = playerHP;

// Sets health bar dimensions
const HEALTHBARWIDTH = 100;
const HEALTHBARHEIGHT = 20;
```

First I created the health bar border, health bar border and the health bar itself. They are rendered as coloured rectangles of a certain width and length. I set the layers so that the health bar border is at the back and is slightly bigger so you can see its edges around the health bar.

```javascript
// Create the health bar border
const healthBarBorder = add([
  rect(HEALTHBARWIDTH + 4, HEALTHBARHEIGHT + 4),
  pos(10, 10),
  z(3), // Layer order for rendering
  color(0, 0, 0),
]);

// Create the background of the health bar
const healthBarBg = add([
  rect(HEALTHBARWIDTH, HEALTHBARHEIGHT),
  pos(12, 12),
  z(4), // Layer order for rendering
  color(79, 75, 75),
]);

// Create the health bar
const healthBar = add([
  rect(playerHP, HEALTHBARHEIGHT),
  pos(12, 12),
  z(5), // Layer order for rendering
  color(92, 204, 12),
]);
```

When the player collides with a spike the player takes 5 damage and the `updateHealthBar` function is called to update the width of the health bar using the player's new hp value. `spikeCooldown` is used to prevent the player from taking spike damage again within 0.5 seconds.

```javascript
let spikeCooldown = false; // Spike collision cooldown flag

// Player and spike collision
onCollide("player", "spike", () => {
  if (!spikeCooldown) {
    spikeCooldown = true;
    player.hurt(5); // Reduce player's hitpoints
    playerHP = player.hp(); // Update player's hitpoints
    updateHealthBar(); // Update health bar width
    wait(0.5, () => {
      spikeCooldown = false; // Cooldown to prevent rapid triggering
    });
  };
});
```

`updateHealthBar` sets the new width of the health bar by finding the proportion of the original width it should be.

```javascript
// Update the health bar width based on player's hitpoints
function updateHealthBar() {
  const newWidth = (playerHP / ORIGINALHP) * HEALTHBARWIDTH;
  healthBar.width = newWidth;
};
```

If the player loses all health points then the levels are replaced with the 'lose' scene. This scene is empty so nothing happens yet when you lose.

```javascript
// Player death event
player.on("death", () => {
  destroy(player); // Destroy the player entity
  wait(0.3, () => {
    go("lose"); // Transition to the "lose" scene
  });
});

scene("lose", () => {
  // Logic and code for the "lose" scene
});
```

### Challenges

Initially, I had planned to set the player's health to a variable which could then be changed and updated as needed. However, this was not possible due to Kaboom's limitations therefore I did it the other way around instead.

## Testing

### Tests

| Test | Instructions                                    | What I expect                                                                                                                                                                                                              | What actually happens                                                    | Pass/Fail |
| ---- | ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ | --------- |
| 1    | Run code and start levels.                      | Correct health bar in the top left of the screen.                                                                                                                                                                          | As expected.                                                             | Pass.     |
| 2    | Walk over spike with player.                    | <ol><li>Player's sprite passes over the spike instead of under.</li><li>The health bar decreases by a fixed amount.</li></ol>                                                                                              | <ol><li>As expected.</li><li>As expected.</li></ol>                      | Pass.     |
| 3    | Repeatedly touch spikes in quick succession.    | <ol><li>Health bar decreases by constant amount each time.</li><li>Health bar does not decrease if the next spike is touched in too quick of a succession.</li><li>Health bar background becomes visible (grey).</li></ol> | <ol><li>As expected.</li><li>As expected.</li><li>As expected.</li></ol> | Pass.     |
| 4    | Touch spikes until the health bar reaches zero. | Game switches to the end screen (which is currently empty).                                                                                                                                                                | As expected.                                                             | Pass.     |
| 5    | Move, shoot and increase levels.                | All functions the same as in [Cycle 4](cycle-1-3.md).                                                                                                                                                                      | As expected.                                                             | Pass.     |

### Images

<div>

<figure><img src="../.gitbook/assets/cycle5healthbarfull.png" alt=""><figcaption><p>Full health bar</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/cycle5healthbardamaged.png" alt=""><figcaption><p>Health bar after standing on multiple spikes</p></figcaption></figure>

</div>

### Evidence

{% embed url="https://youtu.be/_iNutoGY3jY" %}
