# 2.2.8.1 Cycle 8a - Enemy Attacks

## Design

### Objectives

My objectives in this cycle are to:

* [x] Add enemy projectile attacks
* [ ] ~~Enemy projectiles can damage the player~~
* [ ] ~~Add 3 more enemy types which can be put in levels~~
* [ ] ~~Enemy types have different movements, attacks, damage and health~~
* [ ] ~~Make enemy and player bullets able to destroy boxes (which are currently mushrooms)~~

## Development

Enemies were stored as part of the tile map for each level so were spawned when the level was added.

<pre class="language-javascript"><code class="lang-javascript">"1": () => [
<strong>    sprite("ghosty"),
</strong>    area(),
    anchor("center"),
    z(2),
    state("idle", ["idle", "move"]),
    body(),
    health(ENEMY1HP),
    "enemy1",
    "mob",
],
</code></pre>

Function for enemy movement using states.

```typescript
// Function to activate an enemy with state transitions
function activateEnemy1(enem) {
    let _t = 0; // Initialize a time variable

    enem.onStateEnter("idle", (time) => {
    // Wait for a random time and then transition to the "move" state
      wait(time || rand(1, 3), () =>
        enem.enterState("move", rand(1, 3))
      );
    });

  // While in the "idle" state, change the enemy's colour gradually
  enem.onStateUpdate("idle", () => {
    enem.onStateUpdate("idle", () => {
      _t += dt(); // Increment the time variable
      const t = _t % 2 - 1; // Calculate a value for interpolation
      enem.color = lerp(
        rgb(255, 255, 255),
        rgb(128, 128, 128),
        t < 0 ? -t : t
      );
    });

    enem.onStateEnter("move", (time) => {
    // Spawn a bullet and transition back to "idle" state after a specified time
        if (mobs[enem]) {
        spawnEnemyBullet(enem.pos.add(vec2(350, 50)), 1)
        }
        wait(time, () => enem.enterState("idle", rand(1, 5)));
        enem.color = rgb(255, 255, 255);
    });

    enem.onStateUpdate("move", () => {
    // Make the enemy move towards the player's position
      enem.moveTo(player.pos, 100);
    });
  };

    let mobs = level.get("mob"); // Get a list of enemies from the level
    // Call the activateEnemy1 function for each enemy in the list
    for (let i = 0; i < mobs.length; i++) {
        activateEnemy1(mobs[i]);
    };
```

Collision handling and function for enemy bullets.

```typescript
   // Function to spawn an enemy bullet with specific properties
 function spawnEnemyBullet(enemyPos, type) {
    const POINTPLAYER = enemyPos.angle(player.pos.add(vec2(350, 50))) + 180;
    add([
        pos(enemyPos),
        sprite("egg"),
        area(),
        scale(0.65, 0.65),
        color(0, 0, 255),
        anchor("center"),
        z(0),
        rotate(POINTPLAYER + 90),
        move(POINTPLAYER, 500),
        offscreen({ destroy: true }),
        "mob_bullet",
    ]);
};

    onCollide("player_bullet", "mob", (b, m) => {
        destroy(b); // Destroy the player bullet
        if (mobs[m]) {
            mobs[m].health -= bulletDamage; // Reduce the enemy's health
            if (mobs[m].health <= 0) {
                delete mobs[m]; // Remove the enemy from the list if its health reaches zero
                destroy(m); // Destroy the enemy entity
            };
        } else {
            let initialHealth = 0
             // Determine the initial health of the enemy based on its type
            if (m.is("enemy1")) {
                initialHealth = ENEMY1HP;
            } else if (m.is("enemy2")) {
                initialHealth = ENEMY2HP;
            } else if (m.is("enemy3")) {
                initialHealth = ENEMY3HP;
            }
            initialHealth = initialHealth - bulletDamage;
            mobs[m] = { health: initialHealth };  // Update the enemy's health in the list
        };
    });
```

### Challenges

While implementing attempting to implement enemy attacks I experienced a bug where after an enemy died, bullets would continue to fire at the player from the position the enemy died at.

{% embed url="https://youtu.be/Makqcr3obds" %}

Then I was able to make it so that after an enemy died, the remaining enemies stopped firing too.&#x20;

I concluded that this approach was challenging to make work correctly. Therefore I decided to rework my approach to enemies with an object-oriented approach.&#x20;

## Testing

During development, I quickly realised that my approach was buggy and would not be easy to develop long-term, so I decided that approaching the cycle in this way was not a good idea. See [Cycle 8b](cycle-1-8.md) for the restart of Cycle 8.
