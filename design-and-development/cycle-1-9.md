# 2.2.8.2 Cycle 8b - Overhauling Enemies

## Design

### Objectives

My objective in this cycle is to redo enemies using a class system. My objectives are:

* [x] Overhaul the enemy system using an object-oriented approach
* [x] Have 3 enemy types which can be spawned
* [x] Each enemy type has different statistics
* [x] Add enemy projectile attacks

### Usability Features

### Key Variables

| Variable Name                   | Use                                                                                                                                                                                                         |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `chosenLevelFormat`             | An interface defining the structure of a chosen level, including `id`, `layout`, and enemy spawn positions for three different types of enemies.                                                            |
| `randomIndex` and `randomLevel` | Variables used to select a random index from `possibleLevels` array and create a `randomLevel` object based on that index.                                                                                  |
| `position` and `targetPos`      | These variables are used to store positions within the game world. `position` is the initial spawn position for enemies, and `targetPos` is the position that enemies move towards or shoot projectiles at. |
| `direction`                     | A vector representing the direction from one point to another. It's calculated for enemy movement and projectile shooting.                                                                                  |
| `MOVE_SPEED`                    | A constant representing the speed at which enemies move towards their target position.                                                                                                                      |
| `NUM_STEPS`                     | A constant determining the number of steps for smoother enemy movement. It's used within the loop that gradually moves the enemy towards its target.                                                        |
| `BULLET_SPEED`                  | A constant representing the speed of the projectiles that enemies shoot towards the player.                                                                                                                 |
| `latestTargetPos`               | A variable that stores an updated target position. It's used to determine where enemies should shoot projectiles.                                                                                           |
| `min` and `max`                 | These variables are used to define the range for generating random idle times for different enemy types.                                                                                                    |
| `diff`                          | The difference between `max` and `min` values, used for calculating the range of random idle times for enemy types.                                                                                         |

### Pseudocode

```
# Define level configurations
possibleLevels = [ ... ]
fixedLevels = [ ... ]

# Define an interface for level format
chosenLevelFormat = {
    id: number,
    layout: string[],
    enemy1Spawns: [number, number][],
    enemy2Spawns: [number, number][],
    enemy3Spawns: [number, number][],
}

# Initialize an array to store selected levels
chosenLevels = []

# Loop for level selection
for j in range(3):
    # Add the first fixed level
    chosenLevels.push(fixedLevels[0])

    # Loop to randomly select and add possible levels
    for i in range(5):
        randomIndex = getRandomIndex(possibleLevels)
        randomLevel = possibleLevels[randomIndex]
        chosenLevels.push(randomLevel)

    # Add the second fixed level
    chosenLevels.push(fixedLevels[1])

# Define the Enemy class
class Enemy:
    # Constructor
    function Enemy(sprite, type):
        # Initialize properties based on enemy type
        if type is 1:
            # Set properties for enemy type 1
        elif type is 2:
            # Set properties for enemy type 2
        else:
            # Set properties for enemy type 3

    # Method to spawn an enemy
    function spawn(position, player):
        # Create enemy entity
        entity = createEnemyEntity(position)

        # Activate enemy behavior
        activate(entity, player)

    # Method to update enemy health
    function updateHealth(amount):
        # Update current health and handle destruction if necessary

    # Method to destroy an enemy
    function destroy():
        # Remove enemy entity

    # Method to activate enemy behavior
    function activate(entity, player):
        # Set up movement and shooting behavior

    # Method to shoot a projectile
    function shootProjectile(targetPos):
        # Create and launch a projectile entity

# Loop through chosenLevels
for level in chosenLevels:
    # Loop through enemy spawns and create enemies
    for spawn in level.enemy1Spawns:
        enemy = Enemy(sprite1, 1)
        enemy.spawn(spawn, player)

    # Repeat for enemy2Spawns and enemy3Spawns
```

## Development

{% embed url="https://www.youtube.com/watch?v=qQn3Gl8OyVA" %}
Early version of enemies and without bullet collision or damage
{% endembed %}

### Outcome

I modified `possibleLevels` to include an id and the spawn locations of each enemy type for each level.

{% code title="possibleLevels.ts" %}
```javascript
export const possibleLevels = [
    {
        id: 1,
        enemy1Spawns: [
            [100, 200], // [x, y] pair for enemy spawn position
            [500, 250],
        ],
        enemy2Spawns: [
            [400, 200],
        ],
        enemy3Spawns: [
            [100, 400],
        ],
        layout: [
            "====================",
            "=                  =",
            "=                  =",
            "=            +++   =",
            "=            + +   =",
            "=   ^  ++          =",
            "=              +   =",
            "=                  =",
            "=@    ^        ^   =",
            "=          ^   ^   =",
            "=                  =",
            "=                  =",
            "=                  =",
            "=   ^              =",
            "=                  =",
            "====================",
        ],
    },
    ... //more levels
];
```
{% endcode %}

`fixedLevels` was also modified in the same way, however, the enemy spawns are empty for now.

{% code title="fixedLevels.ts" %}
```typescript
export const fixedLevels = [
    {
        id: 100,
        enemy1Spawns: [],
        enemy2Spawns: [],
        enemy3Spawns: [],
        layout: [
            "====================",
            "=                  =",
            "=                  =",
            "=                  =",
            "=                  =",
            "=                  =",
            "=                  =",
            "=        @         =",
            "=                  =",
            "=                  =",
            "=                  =",
            "=                  =",
            "=                  =",
            "=                  =",
            "=                  =",
            "====================",
        ],
    },
```
{% endcode %}

The random level selection had to be modified to handle the storage of objects, which also required an interface.

{% code title="main.ts" %}
```javascript
interface chosenLevelFormat {
    id: number;
    layout: string[];
    enemy1Spawns: [number, number][];
    enemy2Spawns: [number, number][];
    enemy3Spawns: [number, number][];
}

let chosenLevels: chosenLevelFormat[] = [];

// random level selection
for (let j = 0; j < 3; j++) {
    chosenLevels.push({ id: fixedLevels[0].id, layout: fixedLevels[0].layout, enemy1Spawns: fixedLevels[0].enemy1Spawns, enemy2Spawns: fixedLevels[0].enemy2Spawns, enemy3Spawns: fixedLevels[0].enemy3Spawns });
    for (let i = 0; i < 5; i++) {
        let randomIndex = Math.floor(Math.random() * possibleLevels.length);
        let randomLevel = { id: possibleLevels[randomIndex].id, layout: possibleLevels[randomIndex].layout, enemy1Spawns: possibleLevels[randomIndex].enemy1Spawns, enemy2Spawns: possibleLevels[randomIndex].enemy2Spawns, enemy3Spawns: possibleLevels[randomIndex].enemy3Spawns };
        chosenLevels.push(randomLevel);
    }
    chosenLevels.push({ id: fixedLevels[1].id, layout: fixedLevels[1].layout, enemy1Spawns: fixedLevels[1].enemy1Spawns, enemy2Spawns: fixedLevels[1].enemy2Spawns, enemy3Spawns: fixedLevels[1].enemy3Spawns });
}
```
{% endcode %}

The enemy class. It starts with a constructor which sets a number of different parameters depending on the enemy type. It also contains functions that spawn the enemy, move the enemy, spawn enemy bullets, damage the enemy, and destroy the enemy when the health is 0. The benefit of using a class for enemies is that it makes it really easy to add new enemy types in the future because I just need to set what the statistics will be and then its done.

{% code title="main.ts" %}
```typescript
class Enemy {
    sprite: string;
    maxHealth: number;
    currentHealth: number;
    entity: any;
    isMoving: boolean;
    idleTime: number;

    constructor(sprite: string, type: number) {
        if (type === 1) {
            this.maxHealth = 100;
            let min = 2;
            let max = 4;
            let diff = max - min;
            this.idleTime = (Math.random() * diff) + min;
        } else if (type === 2) {
            this.maxHealth = 50;
            let min = 0.5;
            let max = 2;
            let diff = max - min;
            this.idleTime = (Math.random() * diff) + min;
        } else if (type === 3) {
            this.maxHealth = 200;
            let min = 3;
            let max = 4.5;
            let diff = max - min;
            this.idleTime = (Math.random() * diff) + min;
        };
        this.sprite = sprite;
        this.currentHealth = this.maxHealth;
        this.entity = null;
        this.isMoving = false;
    }

    spawn(position: Vec2, player: any) {
        this.entity = add([
            sprite(this.sprite),
            body(),
            area(),
            pos(position),
            anchor("center"),
            z(2),
            "enemy",
        ]);
        this.activate(player);
    }

    updateHealth(amount: number) {
        this.currentHealth -= amount;
        if (this.currentHealth <= 0) {
            this.destroy();
        }
    }

    destroy() {
        destroy(this.entity);
    }

   activate(player: any) {
    this.entity.on("update", async () => {
        if (!this.isMoving) {
            this.isMoving = true;
            this.entity.color = rgb(255, 0, 0);
            const targetPos = player.pos.add(vec2(350, 50));
            const direction = targetPos.sub(this.entity.pos).unit();

            const MOVE_SPEED = 100; // Adjust as needed
            const NUM_STEPS = 20; // Adjust the number of steps for smoother gliding

            for (let i = 0; i < NUM_STEPS; i++) {
                const moveAmount = MOVE_SPEED / NUM_STEPS;
                this.entity.moveBy(direction.scale(moveAmount));
                await wait(0.02); // Adjust the wait period between each step
            };

            await wait(1);
            this.entity.color = rgb(0, 0, 255);
            const latestTargetPos = player.pos.add(vec2(350, 50));
            this.shootProjectile(latestTargetPos);
            await wait(this.idleTime);
            this.isMoving = false;
        }
    });
}

    shootProjectile(targetPos: Vec2) {
        const BULLET_SPEED = 500;
        const direction = targetPos.sub(this.entity.pos);

        add([
            sprite("egg"),
            pos(this.entity.pos),
            area(),
            scale(0.65, 0.65),
            color(255, 0, 0),
            anchor("center"),
            z(2),
            rotate(this.entity.pos.angle(targetPos) + 270),
            move(direction, BULLET_SPEED),
            "enemy_projectile",
            offscreen({ destroy: true }),
        ]);
    }
}
```
{% endcode %}

### Challenges

See [Cycle 8a](cycle-1-8.md) for some of the challenges I experienced which caused me to take this approach.

While creating the enemy movement in the class I had some difficulty getting the enemies to move correctly. Unbeknownst to me there are many movement types (_move_, _moveBy_, _moveTo_, _follow_), which is what was causing the issue.

It took me quite a while to get the enemy class working correctly as I had to deal with a few other bugs too, which was made harder since I hadn't really had much experience with classes before so it was also a bit of a learning experience for me at the same time.

## Testing

### Tests

| Test | Instructions                           | What I expect                                                                                                                                                                                | What actually happens                                                                                                                                                                      | Pass/Fail |
| ---- | -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| 1    | Start first level with T key.          | <ol><li>Enemies spawn at the correct locations.</li></ol><ol start="2"><li>Enemies move towards the player.</li></ol><ol start="3"><li>Enemies pause to shoot before moving again.</li></ol> | <ol><li>Some spawn locations are outside the walls, so when making proper levels I must ensure that spawn locations are within bounds.</li><li>As expected.</li><li>As expected.</li></ol> | Pass.     |
| 2    | Move the player around with WASD keys. | <ol><li>Enemies keep moving towards the new player location, following the player.</li><li>Enemies shoot correctly at the new player location.</li></ol>                                     | <ol><li>As expected.</li><li>As expected.</li></ol>                                                                                                                                        | Pass.     |
| 3    | Increment levels with R key.           | <ol><li>Enemies and bullets from the previous level are deleted</li></ol><ol start="2"><li>New enemies spawn, move and shoot correctly</li></ol>                                             | <ol><li>As expected.</li><li>As expected.</li></ol>                                                                                                                                        | Pass.     |

### Evidence

{% embed url="https://youtu.be/QHwmJjhyhBA" %}
