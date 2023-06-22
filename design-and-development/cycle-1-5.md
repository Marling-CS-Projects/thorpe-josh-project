# 2.2.5 Cycle 5 - Health Bar & Player Death

## Design

### Objectives

My objectives in this cycle are:

* [x] Make the player walk over spikes not under
* [x] Add player health bar
* [x] Standing on spikes deals damage to the player
* [x] Taking damage will lower the health bar
* [x] Reaching 0 health causes the player to die

### Usability Features

### Key Variables

| Variable Name   | Use                                                                                                                                                                             |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| playerHP        | Represents the current hit points of the player. It is initially set to 100 and gets updated when the player gets hurt.                                                         |
| ORIGINALHP      | Represents the starting hit points of the player. It is set to the initial value of `playerHP` and is used to calculate the health bar width.                                   |
| HEALTHBARWIDTH  | Represents the width of the health bar in pixels.                                                                                                                               |
| HEALTHBARHEIGHT | Represents the height of the health bar in pixels.                                                                                                                              |
| healthBarBorder | Represents the health bar's border entity. It is a rectangle with a width and height slightly larger than the health bar itself.                                                |
| healthBarBg     | Represents the background of the health bar. It is a rectangle that provides the visual backdrop for the health bar.                                                            |
| healthBar       | Represents the actual health bar entity. It is a rectangle whose width represents the player's current health.                                                                  |
| spikeCooldown   | Represents a boolean flag indicating whether the player is currently in a cooldown state after colliding with a spike. It prevents the player from getting hurt too frequently. |
| player          | Represents the player entity in the game.                                                                                                                                       |

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

<pre class="language-typescript"><code class="lang-typescript"><strong>...
</strong><strong>
</strong><strong>"^": () => [
</strong>sprite("spike"),
area(),
anchor("center"),
z(0), //layer 0
"entity",
"spike",
],

...

"@": () => [
sprite("bean"),
area(),
anchor("center"),
z(1), //layer 1
body(),
health(playerHP),
"player",
],
</code></pre>

I placed the following code outside of the level scene so that `playerHP` is not reset to 100 at the start of each level. Also, it would be a computational waste to repeatedly define constants.

```typescript
// Sets the player hitpoints and saves the starting point
let playerHP = 100; // Player's current hit points
const ORIGINALHP = playerHP; // Starting hit points

// Sets health bar dimensions
const HEALTHBARWIDTH = 100; // Width of the health bar
const HEALTHBARHEIGHT = 20; // Height of the health bar
```

First I created the health bar border, health bar border and the health bar itself. They are rendered as rectangles of width and length. I set the layers so that the health bar border is at the back and is slightly bigger so you can see its edges. I gave them each a different colour so that they are easily distinguished.

```typescript
// Create the health bar border
const healthBarBorder = add([
  rect(HEALTHBARWIDTH + 4, HEALTHBARHEIGHT + 4), // Rectangle size including border
  pos(10, 10), // Position of the health bar border
  z(3), // Layer order for rendering
  color(0, 0, 0), // Border color
]);

// Create the background of the health bar
const healthBarBg = add([
  rect(HEALTHBARWIDTH, HEALTHBARHEIGHT), // Rectangle size
  pos(12, 12), // Position of the health bar background
  z(4), // Layer order for rendering
  color(79, 75, 75), // Background color
]);

// Create the health bar
const healthBar = add([
  rect(playerHP, HEALTHBARHEIGHT), // Initial width based on player's hit points
  pos(12, 12), // Position of the health bar
  z(5), // Layer order for rendering
  color(92, 204, 12), // Health bar color
]);
```

When the player collides with a spike the player takes 5 damage and the updateHealthBar function is called to update the width of the health bar using the player's new hp value. `spikeCooldown` is used to prevent the player from taking spike damage again within 0.5 seconds.

```typescript
let spikeCooldown = false; // Spike collision cooldown flag

// Player and spike collision
onCollide("player", "spike", () => {
  if (!spikeCooldown) {
    spikeCooldown = true;
    player.hurt(5); // Reduce player's hit points
    playerHP = player.hp(); // Update player's hit points
    updateHealthBar(); // Update health bar width
    wait(0.5, () => {
      spikeCooldown = false; // Cooldown to prevent rapid triggering
    });
  }
});
```

`updateHealthBar` sets the new width of the health bar by finding the proportion of the original width it should be.

```typescript
// Update the health bar width based on player's hit points
function updateHealthBar() {
  const newWidth = (playerHP / ORIGINALHP) * HEALTHBARWIDTH;
  healthBar.width = newWidth;
}

```

If the player loses all health points then the levels are replaced with the 'lose' scene. The 'lose' scene is empty so nothing happens yet when you lose.

```typescript
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

Initially, I had planned to set the player's health to a variable which could then be changed and updated as needed. However, this was not possible so I implemented it the other way around instead.

## Testing

### Tests

| Test | Instructions                                   | What I expect                                                                                                                                                                                                           | What actually happens | Pass/Fail |
| ---- | ---------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------- | --------- |
| 1    | Run code and start levels                      | Correct health bar in the top left of the screen                                                                                                                                                                        | As expected           | Pass      |
| 2    | Walk over spike with player                    | <ol><li>Player's sprite passes over the spike instead of under</li><li>The healthbar decreases</li></ol>                                                                                                                | As expected           | Pass      |
| 3    | Repeatedly touch spikes in quick succession    | <ol><li>Health bar decreases by constant amount each time</li><li>Health bar does not decrease if the next spike is touched in too quick of a succession</li><li>Health bar background becomes visible (grey)</li></ol> | As expected           | Pass      |
| 4    | Touch spikes until the health bar reaches zero | Game switches to the end screen (which is currently empty)                                                                                                                                                              | As expected           | Pass      |
| 5    | Move, shoot and increase levels                | All function as before                                                                                                                                                                                                  | As expected           | Pass      |

### Images

<div>

<figure><img src="../.gitbook/assets/Screenshot 2023-06-16 105519.png" alt=""><figcaption><p>Full health bar</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/Screenshot 2023-06-16 105538.png" alt=""><figcaption><p>Health bar after standing on multiple spikes</p></figcaption></figure>

</div>

### Evidence

{% embed url="https://youtu.be/_iNutoGY3jY" %}
