# 2.2.15 Cycle 15 - Dash Cooldown Bar & Improved UI & Menus

## Design

### Objectives

In this cycle, I will. My objectives in this cycle are:

* [x] Add a dash cooldown bar
* [x] Make boss fight text and new floor text last until the next level
* [x] Move around all the elements of the HUD so that it fits better
* [x] Add a proper win screen
* [x] Add a proper death screen
* [x] Be able to return to the main menu from win or death
* [x] The game should be fully repeatable after restarting
* [x] Make main menu buttons and text bigger
* [x] Add character descriptions in character selection
* [x] Move shopkeeper messages to the bottom of the screen
* [x] Add menu titles to the main menu (including a name for the game)
* [x] Add a background colour to the main menu

#### Smaller Changes

* [x] Add sprites for Sir Galahad and Deadeye Dave
* [x] Ensure that the player is the correct size to fit between boxes in the game

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

The win and lose scenes are very similar since they are both made up of a background, title, text and menu button. Below is the win scene.

```typescript
// win scene -------------------------------------------------------------------
scene("win", () => {
    const menuBackground = add([
        rect(width(), height()),
        pos(width() / 2, height() / 2),
        z(1),
        anchor("center"),
        color(168, 69, 12),
        "menuBackground",
    ]);

    const winTitle = add([
        text("You Win!", {
            size: 80,
        }),
        pos(width() / 2, 180),
        anchor("center"),
        z(10),
        color(22, 219, 55),
    ]);

    const backButton = add([
        text("Return to Main Menu", {
            size: 40,
        }),
        pos(width() / 2, height() - 200),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

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

    backButton.onClick(() => {
        go("mainMenu");
    });
});
```

Below is the lose scene.

```typescript
// lose scene ---------------------------------------------------------------------
scene("lose", () => {

    const menuBackground = add([
        rect(width(), height()),
        pos(width() / 2, height() / 2),
        z(1),
        anchor("center"),
        color(168, 69, 12),
        "menuBackground",
    ]);

    const loseTitle = add([
        text("You Died!", {
            size: 80,
        }),
        pos(width() / 2, 180),
        anchor("center"),
        z(10),
        color(252, 7, 3),
    ]);

    const backButton = add([
        text("Return to Main Menu", {
            size: 40,
        }),
        pos(width() / 2, height() - 200),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    const winText = add([
        text("The Shape King lives on! You made it to floor " + floorNumber + " of 3.\nThe Shape King must perish, try again to defeat him!",
            {
                size: 50,
                align: "center",
            }),
        pos(width() / 2, height() / 2),
        anchor("center"),
        z(10),
    ]);

    backButton.onClick(() => {
        go("mainMenu");
    });

    // Incase an errenous enemies spawn from a boss
    wait(1, () => {
        destroyAll("enemy");
        destroyAll("enemy_bullet")
    });
    
});
```

The dash cooldown bar is created similarly to the player's health bar in [Cycle 5](cycle-1-5.md).

```typescript
const dashCooldownBarBackground = add([
        rect(150, 14),
        pos(1500, 50),
        z(9),
        anchor("left"),
        color(79, 75, 75),
    ]);

    const dashCooldownBarBorder = add([
        rect(154, 16),
        pos(1498, 50),
        anchor("left"),
        z(3),
        color(0, 0, 0),
    ]);

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
let dashCooldown = false;
    //dash
    onMousePress("right", () => {
        if (!dashCooldown) {
            dashCooldown = true;
            dashCooldownBar.width = 0;

            playerSpeed += 300;
            wait(dashDuration, () => {
                playerSpeed -= 300;
            });

            const steps = 100;
            const increment = 150 / steps;
            const stepDuration = dashRecharge / steps * 1000;
            let currentStep = 0;

            function rechargeStep() {
                currentStep++;
                dashCooldownBar.width = increment * currentStep;

                if (currentStep < steps) {
                    setTimeout(rechargeStep, stepDuration);
                } else {
                    dashCooldown = false;
                }
            }

            rechargeStep();

        }
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
    }

        // Function to unlock a weapon
    function unlockWeapon(index) {
        unlockedWeapons.push(weapons[index]);
        unlockedWeapons[index].unlocked = true;
    }

    unlockedWeapons = [];
    unlockWeapon(0); // Unlock pistol
    let currentWeapon = unlockedWeapons[0];


        //Choose random levels
    chosenLevels = [];
    chooseLevels(chosenLevels);
    
    ... // Rest of the main menu code

}
```

#### New Sprites

<div>

<figure><img src="../.gitbook/assets/scarlett.png" alt=""><figcaption><p>Scarlett Blackthorn</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/dave.png" alt=""><figcaption><p>Deadeye Dave</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/galahad.png" alt=""><figcaption><p>Sir Galahad</p></figcaption></figure>

</div>

### Challenges

At first, when trying to resize the player sprites, I tried to use `scale()` when creating the player sprite. However, this made the player collision very strange in that you could clip through the walls for some reason. So to avoid this, I instead shrunk the sprites themselves using an image resizer.

## Testing

### Tests

| Test | Instructions                                                                             | What I expect                                                                                                             | What actually happens | Pass/Fail |
| ---- | ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- | --------------------- | --------- |
| 1    | Start game and look around each menu.                                                    | <ul><li>Text sizes and titles are correct and in the right places.</li></ul>                                              | As expected.          | Pass.     |
| 2    | Go into character selection menu and click on different characters.                      | <ul><li>Character outline is correct size, position and colour.</li><li>Each character is using the new sprite.</li></ul> | As expected.          | Pass.     |
| 3    | Start levels and activate dash.                                                          | Dash cooldown bar becomes empty and replenishes over time.                                                                | As expected.          | Pass.     |
| 4    | Try to dash while bar is recharging.                                                     | Nothing happens. Cannot dash until bar has recharged.                                                                     | As expected.          | Pass.     |
| 5    | Cycle through the levels to reach the win screen.                                        | Win screen appears after the final boss and is correct.                                                                   | As expected.          | Pass.     |
| 6    | Restart game by using the menu button to go back into the main menu. Then play the game. | Game works without issue after being restarted.                                                                           | As expected.          | Pass.     |
| 7    | Get the player killed when health reaches 0.                                             | Lose screen appears with the correct floor number reached.                                                                | As expected.          | Pass.     |
| 8    | Touch shopkeeper.                                                                        | Messages appear in the correct place.                                                                                     | As expected.          | Pass.     |

### Evidence

Put YouTube video testing link here
