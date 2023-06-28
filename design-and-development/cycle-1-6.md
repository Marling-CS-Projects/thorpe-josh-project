# 2.2.6 Cycle 6 - Basic Enemies

## Design

### Objectives

My objective in this cycle is to focus on creating basic enemies. In this cycle, I plan to:

* [x] Add enemies to the level generation so that they can appear in levels
* [x] Enemies will move toward the player
* [x] After a random amount of time, each enemy will stop and then move again
* [x] Enemies have set health and will take damage upon collision with bullets
* [x] Enemies die after all health is lost

### Usability Features

### Key Variables

| Variable Name(s)                       | Use                                                                                                                                                                                                                                      |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ENEMY1HP`, `ENEMY2HP`  and `ENEMY3HP` | These constants represent the initial health points for different enemy types. They are used to set the initial health of enemies and determine when an enemy is defeated.                                                               |
| `bulletDamage`                         | This variable represents the amount of damage inflicted by a bullet. It is used to subtract the bullet's damage from an enemy's health.                                                                                                  |
| `level`                                | This variable represents a level in the game. It is created using the `addLevel` function and contains information about the level's properties, such as tile size, position, and tile definitions.                                      |
| `possibleLevels`                       | This variable is an array that stores different level configurations which the `addLevel` function will access.                                                                                                                          |
| `enemies1`                             | This variable stores an array of enemy objects of type "enemy1" present in the level. It is obtained using the `level.get` method.                                                                                                       |
| `_t`                                   | This variable is a time counter used in the enemy's movement logic. It is incremented by the `dt()` function, which returns the time since the last frame.                                                                               |
| `mobs`                                 | This variable represents a collection of mobile game objects. It is obtained using the `level.get` method and initially contains enemy objects. Later, it also stores additional mobile game objects collided with the player's bullets. |
| `b` and `m`                            | These variables represent the entities involved in the collision (player\_bullet and mob). They are used to perform actions such as destroying the bullet entity and updating the mob's health.                                          |

### Pseudocode

```
// Constants
const ENEMY1HP = 50
const ENEMY2HP = 50
const ENEMY3HP = 50
let bulletDamage = 10

// Level Setup
level = addLevel(possibleLevels[levelId], {
    tileWidth: 58,
    tileHeight: 58,
    pos: vec2(350, 45),
    tiles: {
        "=": () => [...],
        "^": () => [...],
        "+": () => [...],
        "8": () => [...],
        "@": () => [...],
        "1": () => [
            sprite("ghosty"),
            area(),
            anchor("center"),
            z(2),
            state("idle", ["idle", "move"]),
            body(),
            health(ENEMY1HP),
            "enemy1",
            "mob",
        ],
    },
})

// Enemy Movement
function activateEnemy1(enemy1):
    _t = 0

    enemy1.onStateEnter("idle", time):
        wait(time || rand(1, 3), () =>
            enemy1.enterState("move", rand(1, 3))
        )

    enemy1.onStateUpdate("idle"):
        _t += dt()
        t = _t % 2 - 1
        enemy1.color = lerp(
            rgb(255, 255, 255),
            rgb(128, 128, 128),
            t < 0 ? -t : t
        )

    enemy1.onStateEnter("move", time):
        wait(time, () => enemy1.enterState("idle", rand(1, 5)))
        enemy1.color = rgb(255, 255, 255)

    enemy1.onStateUpdate("move"):
        enemy1.moveTo(player.pos, 100)

    return enemy1

// Activate Enemy Movement for each enemy1
enemies1 = level.get("enemy1")
for i = 0 to enemies1.length:
    activateEnemy1(enemies1[i])

// Bullet-Mob Collision
mobs = level.get("mob")
onCollide("player_bullet", "mob", (b, m):
    destroy(b)
    if mobs[m]:
        mobs[m].health -= bulletDamage
        if mobs[m].health <= 0:
            destroy(m)
            delete mobs[m]
    else:
        initialHealth = 0
        if m.is("enemy1"):
            initialHealth = ENEMY1HP
        else if m.is("enemy2"):
            initialHealth = ENEMY2HP
        else if m.is("enemy3"):
            initialHealth = ENEMY3HP
        initialHealth -= bulletDamage
        mobs[m] = { health: initialHealth }
)

```

## Development

### Outcome

```typescript
const ENEMY1HP = 50;
const ENEMY2HP = 50;
const ENEMY3HP = 50;
let bulletDamage = 10;
```

I added a new tile definition to the level generation to add enemies.

```typescript
    const level = addLevel(possibleLevels[levelId], {
        tileWidth: 58,
        tileHeight: 58,
        pos: vec2(350, 45),
        tiles: {
        "=": () => [
            sprite("grass"),
            area(),
            anchor("center"),
            body({ isStatic: true }),
            z(0),
            "entity",
            "wall",
        ],
        "^": () => [...],
        "+": () => [...],
        "8": () => [...],
        "@": () => [...],
        "1": () => [
            sprite("ghosty"),
            area(),
            anchor("center"),
            z(2),
            state("idle", ["idle", "move"]),
            body(),
            health(ENEMY1HP),
            "enemy1",
            "mob",
        ],
        },
    });
```



```typescript
const enemies1 = level.get("enemy1");
    //enemy movement function
    function activateEnemy1(enemy1) {
    let _t = 0;

    enemy1.onStateEnter("idle", (time) => {
      wait(time || rand(1, 3), () =>
        enemy1.enterState("move", rand(1, 3))
      );
    });

    enemy1.onStateUpdate("idle", () => {
      _t += dt();
      const t = _t % 2 - 1;
      enemy1.color = lerp(
        rgb(255, 255, 255),
        rgb(128, 128, 128),
        t < 0 ? -t : t
      );
    });

    enemy1.onStateEnter("move", (time) => {
      wait(time, () => enemy1.enterState("idle", rand(1, 5)));
      enemy1.color = rgb(255, 255, 255);
    });

    enemy1.onStateUpdate("move", () => {
      enemy1.moveTo(player.pos, 100);
    });

    return enemy1;
  }
```

aa

```typescript
    //calls enemy movement for each enemy
    for (let i = 0; i < enemies1.length; i++) {
        activateEnemy1(enemies1[i]);
    }
```

bb

<pre class="language-typescript"><code class="lang-typescript"><strong>const mobs = level.get("mob");
</strong>    onCollide("player_bullet", "mob", (b, m) => {
        destroy(b);
        if (mobs[m]) {
            mobs[m].health -= bulletDamage;
            if (mobs[m].health &#x3C;= 0) {
                destroy(m);
                delete mobs[m];
            }
        } else {
            let initialHealth = 0
            if (m.is("enemy1")) {
                initialHealth = ENEMY1HP;
            } else if (m.is("enemy2")) {
                initialHealth = ENEMY2HP;
            } else if (m.is("enemy3")) {
                initialHealth = ENEMY3HP;
            }
            initialHealth = initialHealth - bulletDamage;
            mobs[m] = { health: initialHealth }; 
        }
    });
</code></pre>

### Challenges

I faced severe challenges with making each enemy act independently. Initially, I attempted to add multiple enemies with one tag and add logic that operates on each one independently. However, I could not get this to work successfully as they would all wait the same random amount of time before stopping and moving again. This was a frustrating challenge as when approaching this problem, it seemed easy to implement. After a few days of trying to get things to work, I decided to take a different approach. I tried a different approach where I added multiple enemies to the level generation which each have a different tag, then I added state transitions for each one individually. However, I soon realised that this was an impractical and clunky approach.

<figure><img src="../.gitbook/assets/cycle6badapproach.png" alt="" width="137"><figcaption><p>This was a bad approach</p></figcaption></figure>

So I went back to my old method and after a while, I managed to get it to work through the use of a function containing each state and calling that function for each enemy, as seen [above](cycle-1-6.md#outcome).

## Testing

### Tests

| Test | Instructions  | What I expect     | What actually happens | Pass/Fail |
| ---- | ------------- | ----------------- | --------------------- | --------- |
| 1    | Run code      | Thing happens     | As expected           | Pass      |
| 2    | Press buttons | Something happens | As expected           | Pass      |

comment on any failed tests

### Evidence

add a youtube link of testing
