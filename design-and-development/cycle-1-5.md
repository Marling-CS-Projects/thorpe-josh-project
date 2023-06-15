# 2.2.5 Cycle 5

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

| Variable Name | Use                   |
| ------------- | --------------------- |
| foo           | does something useful |

### Pseudocode

```
procedure do_something
    
end procedure
```

## Development

### Outcome

I set the layer of the player above the layer of the spikes using the z() property.

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

I placed the following code outside of the level scene so that _playerHP_ is not reset to 100 at the start of each level. Also, it would be a computational waste to repeatedly define constants.

```typescript
//sets the player hitpoints and saves the starting point
let playerHP = 100
const ORIGINALHP = playerHP
//sets health bar dimensions
const HEALTHBARWIDTH = 100
const HEALTHBARHEIGHT = 20
```

First I created the health bar border, health bar border and the health bar itself. They are rendered as rectangles of width and length. I set the layers so that the health bar border is at the back and is slightly bigger so you can see its edges. I gave them each a different colour so that they are easily distinguished.

<pre class="language-typescript"><code class="lang-typescript">    // Create the health bar border
    const healthBarBorder = add([
        rect(HEALTHBARWIDTH + 4, HEALTHBARHEIGHT + 4),
        pos(10, 10),
        z(3),
        color(0, 0, 0),
    ]);
        
    //Create the background of the health bar
    const healthBarBg = add([
<strong>        rect(HEALTHBARWIDTH, HEALTHBARHEIGHT),
</strong>        pos(12, 12),
<strong>        z(4),
</strong>        color(79, 75, 75),
    ]);
        
    // Create the health bar
    const healthBar = add([
        rect(playerHP, HEALTHBARHEIGHT),
        pos(12, 12),
        z(5),
        color(92, 204, 12),
    ]);
</code></pre>

When the player collides with a spike the player takes 5 damage and the updateHealthBar function is called to update the width of the health bar using the player's new hp value. _spikeCooldown_ is used to prevent the player from taking spike damage again within 0.5 seconds.

```typescript
    let spikeCooldown = false
    //player and spike collision
    onCollide("player", "spike", () => {
        if (!spikeCooldown) {
            spikeCooldown = true;
            player.hurt(5);
            playerHP = player.hp();
            updateHealthBar();
            wait(0.5, () => { //cooldown to prevent being triggered too fast repeatedly
                spikeCooldown = false;
            });
        } 
    })
```

updateHealthBar sets the new width of the health bar by finding the proportion of the original width it should be.

```typescript
    //sets the new width
    function updateHealthBar() {
        const newWidth = (playerHP / ORIGINALHP) * HEALTHBARWIDTH; 
        healthBar.width = newWidth;
    }
```

If the player loses all health points then the levels are replaced with the 'lose' scene. The 'lose' scene is empty so nothing happens yet when you lose.

```typescript
    player.on("death", () => {
        destroy(player)
        wait(0.3, () => { //small time delay so player can see empty health bar
            go("lose")  
        });
    })
    
scene("lose", () => {
})
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



### Evidence

add a video link
