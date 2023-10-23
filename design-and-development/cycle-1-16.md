# 2.2.16 Cycle 16 - Boss Health Bar, Automatic Weapons & Fixing Bugs

## Design

### Objectives

In this cycle, I will add some smaller features and fixes. First, I would like to:

* [x] Make the machine pistol and brass spraygun automatic weapons (you can hold down left mouse button to shoot them)
* [x] &#x20;Change the player bullets to rectangles, except for the shotgun which fires small pellets
* [x] Enemies and bosses fire red rectangles

I would also like to add a boss healthbar to display how much of a boss's health is left.

* [x] Add boss healthbar to boss fights

Currently, I have not added many necessary checks to prevent any bugs or issues when carrying out unexpected behaviour. Adding these should help improve the robustness of my game. I will check each box when the issue has been resolved.

* [x] Make sure the player has a character selected before starting the game
* [x] Set the player speed to the default value at the start of each level
* [x] Add a check so that players can only purchase a new weapon once
* [x] You must be in a shop level to buy items

#### Smaller Changes

* [x] Add new sprites for the walls, spikes and boxes
* [x] Adjust some of the enemy spawn locations in levels so that they aren't too close to the player
* [x] Rename Ironclad Carbine to Ironclad Rifle (easier name for players to understand)
* [x] Adjust the coin counter's position
* [x] Fix the dash cooldown bar border
* [x] Fix buying items deducting one less coin than they are supposed to

### Usability Features

The boss's health bar shows players how much health the boss has remaining so they know how close they are to the next level. This can be motivating to know how much health stands in your way.

### Key Variables

| Variable Name                                       | Use                                                                                                                       |
| --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `bossHealthBarBackground` and `bossHealthBarBorder` | These variables define UI elements for the boss's health bar. They represent the bar's background and border.             |
| `bossHealthBar`                                     | This variable represents the bar part of the boss health bar. It is used to display the boss's health during boss fights. |

### Pseudocode

```
// Define key constants
const BOSSHEALTHBARHEIGHT =  // Set the height of the boss health bar
const BOSSSPAWNINGPOS = vec2(1200, 450)  // Set the position for boss enemy spawning

// Define the Player class
class Player:
    // Properties for player character
    pos = vec2(x, y)
    // ...

// Define the Enemy class
class Enemy:
    // Properties for enemies
    // ...

// Define functions for spawning player bullets
function spawnPlayerBullet(truePosition, weapon):
    // Calculate bullet direction
    POINT_CURSOR = angle(truePosition, mousePos()) + 180
    // Calculate bullet spread
    spreadAngle = random() * weapon.accuracy - weapon.accuracy / 2

    // Create and add player bullet entity
    addEntity(
        shape = circle(5),
        pos = truePosition,
        color = color(255, 213, 0),
        // Other properties and behaviors
    )

// Define functions for spawning enemy bullets
function spawnEnemyBullet(targetPos, inaccuracy):
    // Calculate bullet direction
    direction = angle(enemy.pos, targetPos) + 180
    // Calculate bullet spread
    spreadAngle = random() * inaccuracy - inaccuracy / 2

    // Create and add enemy bullet entity
    addEntity(
        shape = rect(7, 15),
        pos = enemy.pos,
        color = color(255, 7, 3),
        // Other properties and behaviors
    )

// Define functions for managing boss UI elements
function addBossUI():
    bossFightText.opacity = 1
    bossHealthBar.height = BOSSHEALTHBARHEIGHT
    bossHealthBar.opacity = 1
    bossHealthBarBackground.opacity = 1
    bossHealthBarBorder.opacity = 1

// Main game loop
while gameIsRunning:
    // Handle player input and movement
    player.handleInput()
    
    // Check for collisions between player bullets and enemies
    checkCollisions(playerBullets, enemies)

    // Check for collisions between enemy bullets and the player
    checkCollisions(enemyBullets, player)

    // Spawn bullets and enemies based on game logic and conditions
    if (chosenLevelIndex === 6):
        spawnBoss("firstboss")
        addBossUI()
    else if (chosenLevelIndex === 13):
        spawnBoss("secondboss")
        addBossUI()
    else if (chosenLevelIndex === 20):
        spawnBoss("thirdboss")
        bossFightText.text = "Final\nBoss!"
        addBossUI()
```

## Development

### Outcome

If the current weapon is the Boomstick then the bullets should be spherical pellets, otherwise, create them as rectangular bullets.

{% code title="spawn bullet.ts" %}
```typescript
export function spawnBullet(truePosition, weapon) {
    // Calculate the angle between the truePosition and the mouse cursor
    const POINT_CURSOR = truePosition.angle(mousePos()) + 180;
    // Generate a random spread angle within the weapon's accuracy
    const spreadAngle = Math.random() * weapon.accuracy - weapon.accuracy / 2;


    if (weapon.name === "Boomstick") {
    // Create a circular bullet for the "Boomstick" weapon
        add([
            pos(truePosition),
            circle(5),
            area(),
            color(255, 213, 0),
            anchor("center"),
            z(2),
            rotate(POINT_CURSOR + 90 + spreadAngle),
            move(POINT_CURSOR + spreadAngle, weapon.bulletSpeed),
            offscreen({ destroy: true }),
            { playerBulletDamage: weapon.bulletDamage },
            "player_bullet",
        ]);
    } else {
    // Create a rectangular bullet for other weapons
        add([
            pos(truePosition),
            rect(7, 15),
            area(),
            color(255, 213, 0),
            anchor("center"),
            z(2),
            rotate(POINT_CURSOR + 90 + spreadAngle),
            move(POINT_CURSOR + spreadAngle, weapon.bulletSpeed),
            offscreen({ destroy: true }),
            { playerBulletDamage: weapon.bulletDamage },
            "player_bullet",
        ]);
    };
};
```
{% endcode %}

I modified bullet spawning in the enemy class to use a rectangle too.

{% code title="enemy class.ts" %}
```typescript
 shootProjectile(targetPos: Vec2) {
        // Calculate the angle between the entity's position and the target position
        let direction = this.entity.pos.angle(targetPos) + 180;
        // Generate a random spread angle within the enemy's accuracy
        const spreadAngle = Math.random() * this.inaccuracy - this.inaccuracy / 2;
        if (this.isAlive) {
            add([
                rect(7,15),
                pos(this.entity.pos),
                area(),
                color(255, 7, 3),
                anchor("center"),
                z(2),
                rotate(this.entity.pos.angle(targetPos) + 270),
                move(direction + spreadAngle, this.bulletSpeed),
                "enemy_bullet",
                { shootDamage: this.shootDamage },
                offscreen({ destroy: true }),
            ]);
        };
    };
```
{% endcode %}

Like the dash cooldown bar and the player healthbar, the boss healthbar is made up of a background, a border, and the actual bar itself. When a boss level starts, the `opacity` of all the objects is set to 1, otherwise, it is 0 and they are invisible in regular levels. When a `player_bullet` collides with an `enemy`, if it is a boss then modify the boss health bar accordingly.

{% code title="main.ts" %}
```typescript
    // Create the boss health bar
    const bossHealthBar = add([
        rect(80, BOSSHEALTHBARHEIGHT),
        pos(160, 240),
        z(10),
        color(255, 0, 0),
        anchor("top"),
        opacity(0),
        "bossHealthBar",

    ]);

    // Create the background for the boss health bar
    const bossHealthBarBackground = add([
        rect(80, BOSSHEALTHBARHEIGHT),
        pos(160, 240),
        z(9),
        color(79, 75, 75),
        anchor("top"),
        opacity(0),
        "bossHealthBar",
    ]);

    // Create the border for the boss health bar
    const bossHealthBarBorder = add([
        rect(80 + 10, BOSSHEALTHBARHEIGHT + 12),
        pos(158, 234),
        anchor("top"),
        opacity(0),
        z(8),
        color(0, 0, 0),
        "bossHealthBar",
    ]);

    // Function to add the boss UI elements
    function addBossUI() {
        bossFightText.opacity = 1;
        bossHealthBar.height = BOSSHEALTHBARHEIGHT;
        bossHealthBar.opacity = 1;
        bossHealthBarBackground.opacity = 1;
        bossHealthBarBorder.opacity = 1;
    };
    
    // Determine what to do for boss levels
    const BOSSSPAWNINGPOS = vec2(1200, 450);

    if (chosenLevelIndex === 6) {
        // Spawn the first boss
        const enemy7 = new Enemy("firstboss", 7);
        enemy7.spawn(BOSSSPAWNINGPOS, player);
        addBossUI();

    } else if (chosenLevelIndex === 13) {
        // Spawn the second boss
        const enemy8 = new Enemy("secondboss", 8);
        enemy8.spawn(BOSSSPAWNINGPOS, player);
       addBossUI();
       
    } else if (chosenLevelIndex === 20) {
        // Spawn the third boss
        const enemy9 = new Enemy("thirdboss", 9);
        enemy9.spawn(BOSSSPAWNINGPOS, player);
        // Update and show boss fight text
        bossFightText.text = "Final\nBoss!"
        addBossUI()
    } else {
        // Hide the boss fight text for non-boss levels
        bossFightText.opacity = 0;
    };

    // Define collision behavior for player bullets and boss enemies
    onCollide("player_bullet", "enemy", (bullet, enemy) => {
        if (enemy.instance.species >= 7) {
            // Calculate the new height of the boss health bar based on enemy's health
            const newHeight = (enemy.instance.currentHealth / enemy.instance.maxHealth) * BOSSHEALTHBARHEIGHT;
            bossHealthBar.height = newHeight;
        };
    });
```
{% endcode %}

I added a check to the start button in character selection which checks that a character has been selected before proceeding.

{% code title="main.ts" %}
```typescript
    startButton.onClick(() => {
        // Checks that a character has been selected before starting the game
        if (selectedCharacterSprite) {
            let chosenLevelIndex = 0;
            go("level", chosenLevelIndex);
        };
    });
```
{% endcode %}

At the start of a new level, the player's speed is set to `originalSpeed` which is the original speed for that sprite. This makes sure that if the player dashes into the next level, the speed is reset properly.

<pre class="language-typescript" data-title="main.ts"><code class="lang-typescript">let originalSpeed = 50;
// Initialised as arbitary value

...

<strong>// Within character select scene
</strong><strong>characterSprite.onClick(() => {
</strong>            originalSpeed = characterSprite.speed;
            ... // Other code
        });
    });
    
...

// Within level scene
playerSpeed = originalSpeed;
</code></pre>

Added a check so that a weapon will not unlock if it has been unlocked already.

{% code title="main.ts" %}
```typescript
// Function to unlock a weapon
    function unlockWeapon(index) {
        if (!weapons[index].unlocked && canUnlockWeapon(index)) {
            ... // Weapon unlock and inventory update code
        };
    };
```
{% endcode %}

Added the `isInShop` flag which makes sure players can only try to buy weapons when in the shop menu.

{% code title="main.ts" %}
```typescript
    // Flag starts as false
    let isInShop = false;

    // When the player touches the shopkeeper, isInShop is true
    onCollide("player", "shopkeeper", () => {
        if (chosenLevelIndex === 7 || chosenLevelIndex === 14) {
            showShopText();
            isInShop = true;
        } else if (chosenLevelIndex === 0) {
            showLoreText();
        };
    });
    
    onCollideEnd("player", "shopkeeper", () => {
        hideShopText();
        hideLoreText();
        isInShop = false;
    });
    

    onKeyPress("o", () => {
        // Must be in the shop to unlock a weapon
        if (isInShop) {
            unlockWeapon(1);
        };
    }); // Unlock machine gun
    
    ... // Other weapon unlock key presses
```
{% endcode %}

#### New Sprites



<div>

<figure><img src="../.gitbook/assets/cycle16wallsprite.png" alt=""><figcaption><p>Wall</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/cycle16boxsprite.png" alt=""><figcaption><p>Box</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/cycle16spikesprite.png" alt=""><figcaption><p>Spike</p></figcaption></figure>

</div>

## Testing

### Tests

| Test | Instructions                                           | What I expect                                                      | What actually happens                                                      | Pass/Fail   |
| ---- | ------------------------------------------------------ | ------------------------------------------------------------------ | -------------------------------------------------------------------------- | ----------- |
| 1    | Fire the clockwork revolver and brass spraygun.        | Can hold down mouse to fire weapons. Releasing mouse stops firing. | As expected.                                                               | Pass.       |
| 2    | Fire the boomstick.                                    | Bullets are circular pellets instead of rectangles.                | As expected.                                                               | Pass.       |
| 3    | Engage in boss fight and kill boss.                    | Health bar depletes as the boss takes damage.                      | As expected, except the boss seems to die before the end of the healthbar. | Borderline. |
| 4    | Dash into next level.                                  | Speed is reset to normal.                                          | As expected.                                                               | Pass.       |
| 5    | Attempt to start game without selecting a character.   | Nothing happens.                                                   | As expected.                                                               | Pass.       |
| 6    | Try to purchase a weapon which is already owned.       | Nothing happens.                                                   | As expected.                                                               | Pass.       |
| 7    | Try to purchase items from the shop in a regular leve. | Nothing happens.                                                   | As expected.                                                               | Pass.       |

### Evidence

{% embed url="https://youtu.be/vBQw5p7JjaQ" %}
