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

* [x] Adjust the size and position of the coin counter
* [x] Move the enemy spawning logic into a separate file

### Usability Features

An inventory system means that players know which keys to press to switch to each weapon. This makes it easier for players to use their weapons more effectively.

### Key Variables

| Variable Name     | Use                                                                                                                                                                                    |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `weapons`         | An array containing objects that represent different weapon types. Each object includes properties like `unlocked`, `name`, `bulletDamage`, `bulletSpeed`, `cooldown`, and `accuracy`. |
| `unlockedWeapons` | An array that stores unlocked weapon objects.                                                                                                                                          |
| `currentWeapon`   | Holds the currently selected weapon from the `unlockedWeapons` array.                                                                                                                  |
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

Each weapon is stored as an object in the `weapons` array which holds all the data for every weapon. Each weapon starts off as locked and can be unlocked at the moment by pressing different keys.

```typescript
const weapons = [
    { unlocked: false, name: "Gear gun", bulletDamage: 20, bulletSpeed: 1200, cooldown: 1, accuracy: 7 }, // Gear gun (pistol)
    { unlocked: false, name: "Brass spraygun", bulletDamage: 5, bulletSpeed: 1000, cooldown: 0.05, accuracy: 15 },  // Brass spraygun (machine gun)
    { unlocked: false, name: "Boomstick", bulletDamage: 10, bulletSpeed: 600, cooldown: 1, accuracy: 20 },  // boomstick (shotgun)
];
```

A weapon is unlocked by pushing it to the `unlockedWeapons` array. The pistol weapon is unlocked by default at the start of the game.

```typescript
    // Function to unlock a weapon
function unlockWeapon(index) {
    unlockedWeapons.push(weapons[index]); // Add to unlockedWeapons array
    unlockedWeapons[index].unlocked = true; // Set unlocked to true in the original weapons array
};

let unlockedWeapons = [];
unlockWeapon(0); // Unlock the pistol when the game starts
currentWeapon = unlockedWeapons[0];
```

I moved the enemy spawning logic to a separate file named `spawn enemies.ts` to help make `main.ts` easier to navigate.

<pre class="language-typescript"><code class="lang-typescript">import { spawnEnemies} from "./spawn enemies";
<strong>
</strong><strong>scene("level", (chosenLevelIndex) => {
</strong><strong>    ... // Rest of the code
</strong>
    spawnEnemies(chosenLevels, chosenLevelIndex, Enemy, player);// Spawn all enemy types
</code></pre>

Bullet spawning code has been modified to use the values from `currentWeapon` which holds the object of the weapon currently in use. If the current weapon is the shotgun then 5 projectiles will be spawned instead of 1.

```typescript
    let gunCooldown = false;
    // Calls spawnBullet on mouse press with cooldown
    onMousePress("left", () => {
        if (!gunCooldown) {
            gunCooldown = true;
            if (currentWeapon === weapons[2]) {
            // Spawn multiple bullets at once
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
        };
    });
    
    // Creates a bullet at the player's position
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
    };
    
    onKeyPress("o", () => {
        unlockWeapon(1);
        inventoryText.updateText();
    }); // Unlocks the machine gun
    onKeyPress("p", () => {
        unlockWeapon(2);
        inventoryText.updateText();
    }); // Unlocks the shotgun
</code></pre>

The current weapon can be switched with the number keys. Each number key corresponds to the order in which the weapons were unlocked. The if statements check that enough weapons have actually been unlocked to use each key, preventing an error.

```typescript
    onKeyPress("1", () => {
        if (unlockedWeapons[0]) { // Checks enough weapons have been unlocked
            currentWeapon = unlockedWeapons[0]; // Switches to the weapon at that position
        };
    });
    onKeyPress("2", () => {
        if (unlockedWeapons[1]) {
            currentWeapon = unlockedWeapons[1];
        };
    });
    onKeyPress("3", () => {
        if (unlockedWeapons[2]) {
            currentWeapon = unlockedWeapons[2]; 
        };
    });
```

The `updateText` function is stored inside `inventoryText`. It adds the name of the unlocked weapon to the inventory.

```typescript
 const inventoryText = add([
        text("Inventory:\n1: -\n2: -\n3: -"), // Starting text
        pos(10, 60),
        z(10), // Displays on top of any other elements
        {
            // Function for updating the inventory text
            updateText: function() {
                const weaponText = unlockedWeapons.map((weapon, index) => {
                    return `${index + 1}: ${weapon.unlocked ? weapon.name : "-"}`;
                }).join("\n");
                this.text = "Inventory:\n" + weaponText;
            };
        };
    ]);
};
```

### Challenges

It was challenging to find out how to update the inventory when a new weapon is unlocked. I was not aware that embedding a function within inventoryText was possible as this was not described in the documentation. Instead, I came across an example of it while viewing some Kaboom code online and made use of it in my code.

## Testing

### Tests

| Test | Instructions                                                                             | What I expect                                                                                                                                                                                                                                                                                                        | What actually happens                                                    | Pass/Fail |
| ---- | ---------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ | --------- |
| 1    | Run code and start game. Attempt to press the 2 and 3 keys and fire the gun by clicking. | <ol><li>Gun should behave the same as pistol.</li></ol><ol start="2"><li>Inventory should only display Gear gun.</li></ol>                                                                                                                                                                                           | <ol><li>As expected.</li><li>As expected.</li></ol>                      | Pass.     |
| 2    | Now try switching weapon after pressing keys 'O' and 'P'. Fire them with by clicking.    | <ol><li>'O' and 'P' unlock the brass spraygun and boomstick and they appear in the inventory after being unlocked.</li></ol><ol start="2"><li>Weapons can be switched between in the same same order as they were unlocked.</li></ol><ol start="3"><li>Accuracy and spread of each weapon are as expected.</li></ol> | <ol><li>As expected.</li><li>As expected.</li><li>As expected.</li></ol> | Pass.     |
| 3    | Start a level and fire each weapon on enemies.                                           | <ol><li>Each weapon appears to do the correct damage.</li></ol><ol start="2"><li>Weapon bullets behave correctly.</li></ol>                                                                                                                                                                                          | <ol><li>As expected.</li><li>As expected.</li></ol>                      | Pass.     |

### Evidence

{% embed url="https://youtu.be/adhG-BJv2YQ" %}
