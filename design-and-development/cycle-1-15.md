# 2.2.15 Cycle 15 - Dash Cooldown Bar & Improved UI & Menus

## Design

### Objectives

In this cycle, my goal is to improve the game UI and menus. My objectives are:

* [x] Add a dash cooldown bar
* [x] Make boss fight text and new floor text last until the next level
* [x] Move around all the elements of the HUD so that it fits better
* [x] Add a proper win screen
* [x] Add a proper death screen
* [x] Be able to return to the main menu from win or death
* [x] The game should be fully repeatable after restarting
* [x] Make main menu buttons and text bigger
* [x] Add character descriptions in character selection
* [x] Add menu titles to the main menu (including a name for the game)
* [x] Add a background colour to the main menu

#### Smaller Changes

* [x] Add a new sprite for Sir Galahad character
* [x] Add a new sprite for Deadeye Dave character
* [x] Move shopkeeper messages to the bottom of the screen
* [x] Reduce the size of player sprites so that they can move in the level easier

### Usability Features

Improving the UI and menus in my game makes player interactions with these more efficient and easier. Furthermore, the dash health bar provides players with additional information while keeping the overall UI as simple as possible.

### Key Variables

| Variable Name                                                   | Use                                                                                                                  |
| --------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `winTitle`, `backButton`, `winText`, `loseTitle` and `loseText` | These variables are used to create text elements for the titles, and informational texts in the win and lose scenes. |
| `menuBackground`                                                | This variable is a background rectangle for all the menu scenes to give them a background colour.                    |
| `dashCooldownBarBackground` and `dashCooldownBarBorder`         | These are graphical elements for the cooldown bar.                                                                   |
| `dashCooldownBar`                                               | Represents the actual cooldown progress bar.                                                                         |
| `dashCooldown`                                                  | A boolean flag to check if the dash ability is on cooldown.                                                          |
| `dashDuration`                                                  | Defines how long a dash lasts.                                                                                       |
| `dashRecharge`                                                  | Defines the total recharge time for the dash ability.                                                                |

### Pseudocode

```
// Define global variables
let coins
let enemiesRemaining
let weapons
let unlockedWeapons
let currentWeapon
let chosenLevels
let dashCooldown
let playerSpeed
let dashDuration
let dashRecharge
let floorNumber

// Define game scenes
scene("win", () => {
    // Create win screen elements
    // Handle "Return to Main Menu" button click
});

scene("lose", () => {
    // Create lose screen elements
    // Handle "Return to Main Menu" button click
    // Cleanup erroneous enemies
});

scene("mainMenu", () => {
    // Initialize game state
    coins = 0
    enemiesRemaining = 0
    unlockedWeapons = []
    
    // Unlock the first weapon (e.g., pistol)
    unlockWeapon(0)

    // Choose random levels
    chooseLevels(chosenLevels)

    // Set up main menu elements
    // Handle menu interactions (e.g., selecting levels, choosing weapons)
});

// Define game loop
loop(() => {
    // Update game logic
    // Handle player input (e.g., dashing)
    // Update enemy behavior
    // Check win or lose conditions
    // Update UI elements (e.g., coin count, cooldown bar)
});

// Define functions
function unlockWeapon(index) {
    // Unlock a weapon and add it to unlockedWeapons
}

function chooseLevels(chosenLevels) {
    // Randomly select levels and add them to chosenLevels
}

function wait(time, callback) {
    // Wait for a specified time and then execute a callback
}

// Define event handlers
onMousePress("right", () => {
    // Handle right mouse button click (e.g., player dash)
});

// Start the game in the main menu scene
go("mainMenu")
```

## Development

### Outcome

The win and lose scenes are very similar since they are both made up of a background, title, text and menu button. Below is how the win scene is set up.

```typescript
// Start of the win scene
scene("win", () => {
    // Create the menu background
    const menuBackground = add([
        rect(width(), height()),
        pos(width() / 2, height() / 2),
        z(1),
        anchor("center"),
        color(168, 69, 12),
        "menuBackground",
    ]);

    // Create the "You Win!" title
    const winTitle = add([
        text("You Win!", {
            size: 80,
        }),
        pos(width() / 2, 180),
        anchor("center"),
        z(10),
        color(22, 219, 55),
    ]);

    // Create the "Return to Main Menu" button
    const backButton = add([
        text("Return to Main Menu", {
            size: 40,
        }),
        pos(width() / 2, height() - 200),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    // Create the win message text
    const winText = add([
        text("Well done adventurer!\nYou defeated the Shape King and his minions and restored balance to the world once more!\nLet us hope that he never returns!",
            {
                size: 50,
                width: 1000,
                align: "center",
            }),
        pos(width() / 2, height() / 2),
        anchor("center"),
        z(10),
    ]);

    // Define an action for the "Return to Main Menu" button
    backButton.onClick(() => {
        go("mainMenu");
    });
});
```

Below is the lose scene, it has very similar elements to those in the win scene.

<pre class="language-typescript"><code class="lang-typescript"><strong>// Start of the lose scene
</strong>scene("lose", () => {
    // Create the menu background
    const menuBackground = add([
        rect(width(), height()),
        pos(width() / 2, height() / 2),
        z(1),
        anchor("center"),
        color(168, 69, 12),
        "menuBackground",
    ]);
    
    // Create the "You Died!" title
    const loseTitle = add([
        text("You Died!", {
            size: 80,
        }),
        pos(width() / 2, 180),
        anchor("center"),
        z(10),
        color(252, 7, 3),
    ]);

    // Create the "Return to Main Menu" button
    const backButton = add([
        text("Return to Main Menu", {
            size: 40,
        }),
        pos(width() / 2, height() - 200),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    // Create the lose message text
    const loseText = add([
        text("The Shape King lives on! You made it to floor " + floorNumber + " of 3.\nThe Shape King must perish, try again to defeat him!",
            {
                size: 50,
                align: "center",
            }),
        pos(width() / 2, height() / 2),
        anchor("center"),
        z(10),
    ]);

    // Define an action for the "Return to Main Menu" button
    backButton.onClick(() => {
        go("mainMenu");
    });

    // Clear any lingering enemies and enemy bullets after 1 second
    wait(1, () => {
        destroyAll("enemy");
        destroyAll("enemy_bullet")
    });
    
});
</code></pre>

The dash cooldown bar is created similarly to the player's health bar in [Cycle 5](cycle-1-4.md).

```typescript
// Create a background for the dash cooldown bar
const dashCooldownBarBackground = add([
    rect(150, 14),
    pos(1500, 50),
    z(9),
    anchor("left"),
    color(79, 75, 75),
]);

// Create a border for the dash cooldown bar
const dashCooldownBarBorder = add([
    rect(154, 16), // The size of the border is slightly larger than the bar
    pos(1498, 50),
    anchor("left"),
    z(3),
    color(0, 0, 0),
]);

// Create the dash cooldown bar itself
const dashCooldownBar = add([
    rect(150, 14),
    pos(1500, 50),      
    z(10),               
    color(0, 0, 255),
    anchor("left"),
]);
```

The dashing code was modified to include the dash cooldown bar. When a dash has ended, the cooldown bar's width is set near zero and then set to increasingly greater widths in set steps, making it look like it is moving smoothly.

```typescript
// Initialize the dashCooldown flag to false
let dashCooldown = false;
    // Dash ability when the right mouse button is pressed
    onMousePress("right", () => {
        if (!dashCooldown) {
            dashCooldown = true;
            dashCooldownBar.width = 0;
            // Increase player's speed during the dash
            playerSpeed += 300;
             // Wait for the dashDuration and then reset player's speed
            wait(dashDuration, () => {
                playerSpeed -= 300;
            });
            
            // Configure how the dash cooldown bar will refill
            const steps = 100;
            const increment = 150 / steps;
            const stepDuration = dashRecharge / steps * 1000;
            let currentStep = 0;

            // Define a function for each step of the recharge
            function rechargeStep() {
                currentStep++;
                dashCooldownBar.width = increment * currentStep;

                if (currentStep < steps) {
                    // Schedule the next step after stepDuration
                    setTimeout(rechargeStep, stepDuration);
                } else {
                    dashCooldown = false;
                };
            };
            // Start the recharge process
            rechargeStep();
        };
    });
```

To make the game replayable after dying or winning, I added/moved code which initialises a lot of aspects.

```typescript
scene("mainMenu", () => {

    coins = 0;
    enemiesRemaining = 0;

    // Lock every weapon
    for (const weapon of weapons) {
        weapon.unlocked = false;
    };

    // Function to unlock a weapon
    function unlockWeapon(index) {
        unlockedWeapons.push(weapons[index]);
        unlockedWeapons[index].unlocked = true;
    };

    unlockedWeapons = [];
    unlockWeapon(0); // Unlock pistol
    let currentWeapon = unlockedWeapons[0];


    //Choose random levels
    chosenLevels = [];
    chooseLevels(chosenLevels);
    
    ... // Rest of the main menu code before the game starts

};
```

#### New Sprites

<div align="center" data-full-width="false">

<figure><img src="../.gitbook/assets/cycle15davesprite.png" alt=""><figcaption><p>Deadeye Dave</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/cycle15galahadsprite.png" alt=""><figcaption><p>Sir Galahad</p></figcaption></figure>

</div>

### Challenges

For my initial attempt at resizing the player sprites, I tried using `scale()` to reduce the size when the player was added. However, this made the player collision very strange in that you could clip into the walls and other objects for some reason. To avoid this, I instead shrunk the sprites themselves using an online image resizing tool and added them to the game as separate sprites which are used for the player entity, while the original sizes remain in the character selection display.

## Testing

### Tests

| Test | Instructions                                                                             | What I expect                                                                                                                                     | What actually happens                                                                                                                                                | Pass/Fail                                   |
| ---- | ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| 1    | Start game and look around each menu.                                                    | Text sizes and titles are correct and in the right places.                                                                                        | As expected.                                                                                                                                                         | Pass.                                       |
| 2    | Go into character selection menu and click on different characters.                      | <ul><li>Character outline is correct size, position and colour.</li><li>Each character is using the new sprite.</li></ul>                         | <ul><li>As expected.</li><li>As expected.</li></ul>                                                                                                                  | Pass.                                       |
| 3    | Start levels and activate dash.                                                          | <ul><li>Dash cooldown bar is added correctly and in the right place.</li><li>Dash cooldown bar becomes empty and replenishes over time.</li></ul> | <ul><li>The positon of the dash cooldown bar, but I noticed that if you look very closely, the border is not quite even on all sides.</li><li>As expected.</li></ul> | <ul><li>Borderline.</li><li>Pass.</li></ul> |
| 4    | Try to dash while bar is recharging.                                                     | Nothing happens. Cannot dash until bar has recharged.                                                                                             | As expected.                                                                                                                                                         | Pass.                                       |
| 5    | Cycle through the levels to reach the win screen.                                        | Win screen appears after the final boss and is correct.                                                                                           | As expected.                                                                                                                                                         | Pass.                                       |
| 6    | Restart game by using the menu button to go back into the main menu. Then play the game. | Game works without issue after being restarted.                                                                                                   | As expected.                                                                                                                                                         | Pass.                                       |
| 7    | Get the player killed when health reaches 0.                                             | Lose screen appears with the correct floor number reached.                                                                                        | As expected.                                                                                                                                                         | Pass.                                       |
| 8    | Touch shopkeeper.                                                                        | Messages appear in the correct place.                                                                                                             | As expected.                                                                                                                                                         | Pass.                                       |

I noticed a small issue with the border of the dash cooldown bar not quite being in the right position. This will be an easy fix in the next cycle since I just need to change some values.

### Evidence

{% embed url="https://youtu.be/OFSUixsebSE" %}
