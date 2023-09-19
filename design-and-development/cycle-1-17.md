# 2.2.16 Cycle 16 - Smaller Features & Fixing Errors

## Design

### Objectives

In this cycle, I will add some smaller features and fixes. First, I would like to:

* [x] &#x20;Change the player bullets to rectangles, except for the shotgun which fires small pellets
* [x] Enemies and bosses fire red rectangles
* [x] Make the machine pistol and brass spraygun automatic weapons (you can hold down left mouse button to shoot them)

I would also like to add a boss healthbar to display how much of a boss's health is left.

* [x] Add boss healthbar to boss fights

Currently, my game is missing many checks which causes bugs or crashes when carrying out unexpected behaviour. I will fix these issues in this cycle, which should help improve the robustness of my game.

* [x] Starting the game without selecting a character throws an error
* [x] Dashing into the next level does not reset the speed after the dash
* [x] Players can purchase weapons which they already own, which get put in the inventory twice
* [x] Purchasing items will deduct one less coin than required
* [x] Players can purchase items from the shop even when not in a shop level

#### Smaller Changes

* [x] Add new sprites for the walls, spikes and boxes
* [x] Further reduce the number of spikes and boxes in levels
* [x] Adjust some of the enemy spawn locations in levels so that they are not too close to the player
* [x] Rename Ironclad Carbine to Ironclad Rifle (easier name for players to understand)
* [x] Adjust coin counter position
* [x] Correct the dash cooldown bar border

### Usability Features

### Key Variables

| Variable Name      | Use                                                                                                                                        |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `BOSSSPAWNINGPOS`  | Represents the spawning position for boss enemies in specific levels.                                                                      |
| `chosenLevelIndex` | A variable that holds the current level index. It's used to determine which boss enemy to spawn and when to show boss-related UI elements. |

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
    const POINT_CURSOR = truePosition.angle(mousePos()) + 180;

    const spreadAngle = Math.random() * weapon.accuracy - weapon.accuracy / 2;


    if (weapon.name === "Boomstick") {
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
    }
};
```
{% endcode %}

I modified bullet spawning in the enemy class to use a rectangle too.

{% code title="enemy class.ts" %}
```typescript
 shootProjectile(targetPos: Vec2) {
        let direction = this.entity.pos.angle(targetPos) + 180;
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

<pre class="language-typescript" data-title="main.ts"><code class="lang-typescript"><strong>const bossHealthBar = add([
</strong>        rect(80, BOSSHEALTHBARHEIGHT),
        pos(160, 240),
        z(10),
        color(255, 0, 0),
        anchor("top"),
        opacity(0),
        "bossHealthBar",

    ]);

    const bossHealthBarBackground = add([
        rect(80, BOSSHEALTHBARHEIGHT),
        pos(160, 240),
        z(9),
        color(79, 75, 75),
        anchor("top"),
        opacity(0),
        "bossHealthBar",
    ]);

    const bossHealthBarBorder = add([
        rect(80 + 10, BOSSHEALTHBARHEIGHT + 12),
        pos(158, 234),
        anchor("top"),
        opacity(0),
        z(8),
        color(0, 0, 0),
        "bossHealthBar",
    ]);

    function addBossUI() {
        bossFightText.opacity = 1;
        bossHealthBar.height = BOSSHEALTHBARHEIGHT;
        bossHealthBar.opacity = 1;
        bossHealthBarBackground.opacity = 1;
        bossHealthBarBorder.opacity = 1;
    };
    
    // What to do for boss levels
    const BOSSSPAWNINGPOS = vec2(1200, 450);

    if (chosenLevelIndex === 6) {
        // Spawn the boss
        const enemy7 = new Enemy("firstboss", 7);
        enemy7.spawn(BOSSSPAWNINGPOS, player);
        addBossUI();
        

    } else if (chosenLevelIndex === 13) {
        // Spawn the boss
        const enemy8 = new Enemy("secondboss", 8);
        enemy8.spawn(BOSSSPAWNINGPOS, player);
       addBossUI();
    } else if (chosenLevelIndex === 20) {
        // Spawn the boss
        const enemy9 = new Enemy("thirdboss", 9);
        enemy9.spawn(BOSSSPAWNINGPOS, player);
        // Update and show boss fight text
        bossFightText.text = "Final\nBoss!"
        addBossUI()
    } else {
        // Hide boss fight text
        //bossFightText.opacity = 0;
    };

    onCollide("player_bullet", "enemy", (bullet, enemy) => {
        if (enemy.instance.species >= 7) {
            const newHeight = (enemy.instance.currentHealth / enemy.instance.maxHealth) * BOSSHEALTHBARHEIGHT;
            bossHealthBar.height = newHeight;
        };
    });
</code></pre>

I added a check to the start button in character selection which checks that a character has been selected before proceeding.

{% code title="main.ts" %}
```typescript
    startButton.onClick(() => {
        if (selectedCharacterSprite) {
            let chosenLevelIndex = 0;
            go("level", chosenLevelIndex);
        }
    });
```
{% endcode %}

At the start of a  new level, the player's speed is set to `originalSpeed` which is the original speed for that sprite. This makes sure that if the player dashes into the next level, the speed is reset properly.

<pre class="language-typescript" data-title="main.ts"><code class="lang-typescript">let originalSpeed = 50; // Initialised as arbitary vale

...

<strong>// Within character select scene
</strong><strong>characterSprite.onClick(() => {
</strong>            originalSpeed = characterSprite.speed;
            ... // Other code
        });
    });
    
...

playerSpeed = originalSpeed; // Within level scene
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
let isInShop = false;


onCollide("player", "shopkeeper", () => {
        if (chosenLevelIndex === 7 || chosenLevelIndex === 14) {
            showShopText();
            isInShop = true;
        } else if (chosenLevelIndex === 0) {
            showLoreText();
        }
    })
    onCollideEnd("player", "shopkeeper", () => {
        hideShopText();
        hideLoreText();
        isInShop = false;
    })
    

onKeyPress("o", () => {
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

| Test | Instructions                                    | What I expect                                                      | What actually happens | Pass/Fail |
| ---- | ----------------------------------------------- | ------------------------------------------------------------------ | --------------------- | --------- |
| 1    | Fire the clockwork revolver and brass spraygun. | Can hold down mouse to fire weapons. Releasing mouse stops firing. | As expected.          | Pass.     |
| 2    | Fire the boomstick.                             | Bullets are circular pellets instead of rectangles.                | As expected.          | Pass.     |
| 3    |                                                 |                                                                    |                       |           |
| 4    |                                                 |                                                                    |                       |           |
| 5    |                                                 |                                                                    |                       |           |

### Evidence

{% embed url="https://youtu.be/vBQw5p7JjaQ" %}
