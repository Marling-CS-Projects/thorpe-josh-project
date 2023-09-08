# 2.2.10 Cycle 10 - Different Weapons & Inventory

## Design

### Objectives

In this cycle, I aim to set up a proper weapon and inventory system. My objectives are to:

* [x] Change player bullet damage to be stored on each individual bullet
* [x] Add 'Brass Spraygun' (machine gun with high rate of fire but low damage)
* [x] Add 'Boomstick' (shotgun which fires multiple bullets at once)
* [x] The current shooting is now the 'Gear gun' which is the weapon you start with
* [x] Modify the statistics of each gun to balance them somewhat (I can properly balance everything towards the end of development)
* [x] Guns can be changed with the number keys
* [x] The inventory system displays your weapons on the number keys As you unlock new weapons they appear in the inventory in that order (i.e., the first weapon unlocked is key 1, the second is key 2, etc.)

#### Smaller Changes

* [x] Change the size and position of the coin counter
* [x] Move the enemy spawning logic into a separate file

### Usability Features

### Key Variables

| Variable Name     | Use                                                                                                                                                                                    |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `weapons`         | An array containing objects that represent different weapon types. Each object includes properties like `unlocked`, `name`, `bulletDamage`, `bulletSpeed`, `cooldown`, and `accuracy`. |
| `unlockedWeapons` | An array that stores unlocked weapon objects.                                                                                                                                          |
| `currentWeapon`   | Holds the currently selected weapon from the `unlockedWeapons` array.                                                                                                                  |
| `gunCooldown`     | A boolean variable used to manage the cooldown between shots.                                                                                                                          |
| `POINT_CURSOR`    | Represents the angle towards which bullets are aimed.                                                                                                                                  |
| `spreadAngle`     | A random angle used to simulate bullet inaccuracy.                                                                                                                                     |
| `inventoryText`   | Represents the text entity displaying the player's unlocked weapons.                                                                                                                   |

### Pseudocode

```
// Define weapon types
const weapons = [
    { unlocked: false, name: "Gear gun", bulletDamage: 20, bulletSpeed: 1200, cooldown: 1, accuracy: 7 },
    { unlocked: false, name: "Brass spraygun", bulletDamage: 5, bulletSpeed: 1000, cooldown: 0.05, accuracy: 15 },
    { unlocked: false, name: "Boomstick", bulletDamage: 10, bulletSpeed: 600, cooldown: 1, accuracy: 20 }
];

// Define unlocked weapons array
let unlockedWeapons = [];

// Define current weapon
let currentWeapon;

// Define gun cooldown status
let gunCooldown = false;

// Function to unlock a weapon
function unlockWeapon(index) {
    weapons[index].unlocked = true;
    unlockedWeapons.push(weapons[index]);
}

// Function to create and spawn a bullet
function spawnBullet(truePosition, weapon) {
    // Calculate aiming angle and spread angle
    const POINT_CURSOR = angleBetweenPositions(truePosition, mousePosition());
    const spreadAngle = randomSpreadAngle(weapon.accuracy);

    // Create bullet entity with appropriate properties
    createBulletEntity(truePosition, POINT_CURSOR, spreadAngle, weapon.bulletDamage);
}

// Define inventory text entity
const inventoryText = createInventoryTextEntity();

// Event listeners for unlocking weapons
onKeyPress("o", () => {
    unlockWeapon(1);
    updateInventoryText();
}); // Unlock machine gun
onKeyPress("p", () => {
    unlockWeapon(2);
    updateInventoryText();
}); // Unlock shotgun

// Event listeners for selecting weapons
onKeyPress("1", () => { selectWeapon(0); });
onKeyPress("2", () => { selectWeapon(1); });
onKeyPress("3", () => { selectWeapon(2); });

// Event listener for shooting
onMousePress("left", () => {
    if (!gunCooldown) {
        gunCooldown = true;
        if (currentWeapon === weapons[2]) {
            // Spawn multiple bullets for shotgun
            for (let i = 0; i < 5; i++) {
                spawnBullet(playerPosition().add(vec2(350, 50)), currentWeapon);
            }
        } else {
            spawnBullet(playerPosition().add(vec2(350, 50)), currentWeapon);
        }
        wait(currentWeapon.cooldown, () => {
            gunCooldown = false;
        });
    }
});
```

## Development

### Outcome

Each weapon is stored as an object in the `weapons` array which holds all the data for every weapon. Each weapon starts off being locked but can be unlocked for now with specific keys.

```typescript
const weapons = [
    { unlocked: false, name: "Gear gun", bulletDamage: 20, bulletSpeed: 1200, cooldown: 1, accuracy: 7 }, // Gear gun (pistol)
    { unlocked: false, name: "Brass spraygun", bulletDamage: 5, bulletSpeed: 1000, cooldown: 0.05, accuracy: 15 },  // Brass spraygun (machine gun)
    { unlocked: false, name: "Boomstick", bulletDamage: 10, bulletSpeed: 600, cooldown: 1, accuracy: 20 },  // boomstick (shotgun)
];
```

The pistol weapon is unlocked at the start of the game. When I add character selection in a future cycle, each character will start with a different weapon.

```typescript
    // Function to unlock a weapon
function unlockWeapon(index) {
    unlockedWeapons.push(weapons[index]);
    unlockedWeapons[index].unlocked = true;
}

let unlockedWeapons = [];
unlockWeapon(0); // Unlock pistol
currentWeapon = unlockedWeapons[0];
```

I moved the enemy spawning logic to a separate file to make `main.ts` easier to navigate.

<pre class="language-typescript"><code class="lang-typescript">import { spawnEnemies} from "./spawn enemies";
<strong>
</strong><strong>scene("level", (chosenLevelIndex) => {
</strong><strong>    ... //rest of code
</strong>
    spawnEnemies(chosenLevels, chosenLevelIndex, Enemy, player); //spawn in all enemy types

</code></pre>

Bullet spawning code has been modified to use the values from `currentWeapon` which holds the object of the weapon currently in use. If the current weapon is the shotgun then 5 projectiles will be spawned instead of 1.

```typescript
    let gunCooldown = false;
    //calls spawnBullet on mouse press with cooldown
    onMousePress("left", () => {
        if (!gunCooldown) {
            gunCooldown = true;
            if (currentWeapon === weapons[2]) {
                spawnBullet(player.pos.add(vec2(350, 50)), currentWeapon);
                spawnBullet(player.pos.add(vec2(350, 50)), currentWeapon);
                spawnBullet(player.pos.add(vec2(350, 50)), currentWeapon);
                spawnBullet(player.pos.add(vec2(350, 50)), currentWeapon);
                spawnBullet(player.pos.add(vec2(350, 50)), currentWeapon);
            } else {
                spawnBullet(player.pos.add(vec2(350, 50)), currentWeapon);
            };
            wait(currentWeapon.cooldown, () => {
                gunCooldown = false;
            });
        }
    });
    
    //creates a bullet at the player's position
    function spawnBullet(truePosition, weapon) {
        const POINT_CURSOR = truePosition.angle(mousePos()) + 180;

        const spreadAngle = Math.random() * weapon.accuracy - weapon.accuracy / 2;
        
        add([
            pos(truePosition),
            sprite("egg"),
            area(),
            scale(0.65, 0.65),
            color(127, 127, 255),
            anchor("center"),
            z(2),
            rotate(POINT_CURSOR + 90 + spreadAngle),
            move(POINT_CURSOR + spreadAngle , weapon.bulletSpeed),
            offscreen({ destroy: true }),
            { playerBulletDamage: weapon.bulletDamage },
            "player_bullet",
        ]);
    };
```

The machine gun and shotgun can be unlocked with the 'o' and 'p' keys. The `unlockWeapon` function had to be repeated as it could not be called from inside a scene. Unlocking a weapon also adds it to the inventory display.

<pre class="language-typescript"><code class="lang-typescript"><strong>    // Function to unlock a weapon
</strong>    function unlockWeapon(index) {
        weapons[index].unlocked = true;
        unlockedWeapons.push(weapons[index]);
    }
    
    onKeyPress("o", () => {
        unlockWeapon(1);
        inventoryText.updateText();
    }); // Unlock machine gun
    onKeyPress("p", () => {
        unlockWeapon(2);
        inventoryText.updateText();
    }); // Unlock shotgun
</code></pre>

Weapons can be switched to with the number keys depending on the order they were unlocked. The if statements check that enough weapons have actually been unlocked to use each key, preventing an error.

```typescript
    onKeyPress("1", () => {
        if (unlockedWeapons[0]) {
            currentWeapon = unlockedWeapons[0];
        }
    })
    onKeyPress("2", () => {
        if (unlockedWeapons[1]) {
            currentWeapon = unlockedWeapons[1];
        }
    })
    onKeyPress("3", () => {
        if (unlockedWeapons[2]) {
            currentWeapon = unlockedWeapons[2];
        }
    })
```

The `updateText` function is stored inside `inventoryText`. It adds the name of the unlocked weapon to the inventory.

```typescript
 const inventoryText = add([
        text("Inventory:\n1: -\n2: -\n3: -"),
        pos(10, 60),
        z(10),
        {
            updateText: function() {
                const weaponText = unlockedWeapons.map((weapon, index) => {
                    return `${index + 1}: ${weapon.unlocked ? weapon.name : "-"}`;
                }).join("\n");
                this.text = "Inventory:\n" + weaponText;
            }
        }
    ]);
}
```

### Challenges

It took me a while to wrap my head around how I was going to implement the inventory system.

## Testing

### Tests

| Test | Instructions                                                                             | What I expect                                                                                                                                                                                                                                                                  | What actually happens | Pass/Fail |
| ---- | ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------- | --------- |
| 1    | Run code and start game. Attempt to press the 2 and 3 keys and fire the gun by clicking. | <ul><li>Gun should behave the same as pistol.</li><li>Inventory should only display Gear gun.</li></ul>                                                                                                                                                                        | As expected.          | Pass.     |
| 2    | Now try switching weapon after pressing keys 'o' and 'p'. Fire them with by clicking.    | <ul><li>'o' and 'p' unlock the brass spraygun and boomstick and they appear in the inventory after being unlocked.</li><li>Weapons can be switched between in the same same order as they were unlocked.</li><li>Accuracy and spread of each weapon are as expected.</li></ul> | As expected.          | Pass.     |
| 3    | Start a level and fire each weapon on enemies.                                           | <ul><li>Each weapon appears to do the correct damage.</li><li>Weapon bullets behave correctly.</li></ul>                                                                                                                                                                       | As expected.          | Pass.     |

### Evidence

{% embed url="https://youtu.be/adhG-BJv2YQ" %}
