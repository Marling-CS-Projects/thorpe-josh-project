# 2.2.14 Cycle 14 - Boss Fights

## Design

### Objectives

In this cycle, I will. My objectives in this cycle are:

* [ ] Add spikes to boss levels
* [x] Rebalance the guns and enemies
* [ ] Add boss enemies as new enemy types to the enemy class
* [ ] Bosses are bigger and stronger than normal enemies
* [ ] Boss 2 shoots in bursts
* [ ] Boss 3 shoots like a shotgun
* [ ] Bosses can spawn more enemies around them

#### Smaller Changes

* [ ] Reduce the number of spikes and boxes on floor 3 because it is too difficult to move around at the moment
* [ ] Add spikes to boss levels
* [ ] Make the background for the introduction message partially transparent

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

explain the code

```typescript
// Some code
```

explain some more

```typescript
// Some more code
```

### Challenges

Initially, I planned to implement bosses using a separate boss class with the special boss behaviour which I could then call when a boss level started.

{% code title="boss class.ts" %}
```typescript
import { updateCoinCounter } from "./main";
import { Enemy } from "./enemy class";

export class Boss {
    sprite: string;
    maxHealth: number;
    currentHealth: number;
    entity: any;
    isMoving: boolean;
    idleTime: number;
    shootDamage: number;
    isAlive: boolean;
    numSteps: number;
    moveSpeed: number;
    accuracy: number;
    bulletSpeed: number;
    type: number;

    constructor(sprite: string, type: number) {
        // Initialize properties based on the enemy type
        if (type === 1) { // Boss type 1
            this.maxHealth = 300;
            this.idleTime = (Math.random() * (4 - 2.5) + 2.5);
            this.shootDamage = 25;
            this.numSteps = 20;
            this.moveSpeed = 100;
            this.accuracy = 3;
            this.bulletSpeed = 800;
            this.type = 1;
        } else if (type === 2) { // Boss type 2
            this.maxHealth = 500;
            this.idleTime = (Math.random() * (4 - 2.5) + 2.5);
            this.shootDamage = 10;
            this.numSteps = 20;
            this.moveSpeed = 100;
            this.accuracy = 6;
            this.bulletSpeed = 800;
            this.type = 2;
        } else if (type === 3) { // Boss type 3
            this.maxHealth = 1000;
            this.idleTime = (Math.random() * (3.5 - 2) + 2);
            this.shootDamage = 12;
            this.numSteps = 20;
            this.moveSpeed = 100;
            this.accuracy = 20;
            this.bulletSpeed = 800;
            this.type = 3;
        };
        this.sprite = sprite;
        this.currentHealth = this.maxHealth;
        this.entity = null;
        this.isMoving = false;
        this.isAlive = true;
    }

    spawn(position: Vec2, player: any) {
        this.entity = add([
            sprite(this.sprite),
            body(),
            area(),
            pos(position),
            anchor("center"),
            z(2),
            "boss",
        ]);
        this.entity.instance = this;
        this.activate(player);
    }

    updateHealth(amount: number) {
        this.currentHealth -= amount;
        if (this.currentHealth <= 0) {
            this.destroy();
        }
    }

    destroy() {
        this.isAlive = false;
        if (this.type === 1) {
            for (let i = 0; i < 10; i++) {
                updateCoinCounter(); //will give 10 coins
            }
        } else if (this.type === 2) {
            for (let i = 0; i < 15; i++) {
                updateCoinCounter(); //will give 15 coins
            }
        }
        destroy(this.entity);
    }

    activate(player: any) {
            this.entity.on("update", async () => {
                if (!this.isMoving) {
                    this.isMoving = true;
                    this.entity.color = rgb(255, 0, 0);
                    const targetPos = player.pos.add(vec2(350, 50));
                    const direction = targetPos.sub(this.entity.pos).unit();
    
                    for (let i = 0; i < this.numSteps; i++) {
                        const moveAmount = this.moveSpeed / this.numSteps;
                        this.entity.moveBy(direction.scale(moveAmount));
                        await wait(0.02); // Adjust the wait period between each step
                    };
    
                    await wait(1);
                    this.entity.color = rgb(0, 0, 255);
                    const latestTargetPos = player.pos.add(vec2(350, 50));
                    //this.shootProjectile(latestTargetPos);
                    await wait(this.idleTime);
                    this.isMoving = false;
                }
            });
        }

    shootProjectile(targetPos: Vec2) {
        const spreadAngle = Math.random() * this.accuracy - this.accuracy / 2;
        const direction = targetPos.sub(this.entity.pos);
        if (this.isAlive) {
            add([
                sprite("egg"),
                pos(this.entity.pos),
                area(),
                scale(0.65, 0.65),
                color(255, 0, 0),
                anchor("center"),
                z(2),
                rotate(this.entity.pos.angle(targetPos) + 270),
                move(direction + spreadAngle, this.bulletSpeed),
                "enemy_bullet",
                { shootDamage: this.shootDamage },
                offscreen({ destroy: true }),
            ]);
        }
    }

    spawnMinions(player: any) {
        // Define the positions for the minions relative to the boss
        const minionPositions: vec2[] = [
            vec2(50, 0),
            vec2(-50, 0),
        ];

        // Spawn minions at specified positions
        for (const position of minionPositions) {
            if (this.type === 1) {
                const enemy2 = new Enemy("bobo", 2);
                enemy2.spawn(this.entity.pos.add(position), player)
            } else if (this.type === 2) {
                const enemy4 = new Enemy("dino", 4);
                enemy4.spawn(this.entity.pos.add(position), player)
            } else if (this.type === 3) {
                const enemy6 = new Enemy("gigagantrum", 6);
                enemy6.spawn(this.entity.pos.add(position), player)
            };
        }
    }
}
```
{% endcode %}

However, I kept getting strange behaviour and errors despite most of the code being duplicated from the Enemy class. This confused me and I realised that it would be much simpler to use the Enemy class for bosses and add a few special functions which only the boss enemies call. Below is a video of my first attempt at bosses.

{% embed url="https://youtu.be/x5ntEovn-fM" %}

## Testing

### Tests

| Test | Instructions   | What I expect      | What actually happens | Pass/Fail |
| ---- | -------------- | ------------------ | --------------------- | --------- |
| 1    | Run code.      | Thing happens.     | As expected.          | Pass.     |
| 2    | Press buttons. | Something happens. | As expected.          | Pass.     |

Comment on any failed tests and how you plan to resolve them

### Evidence

Put YouTube video testing link here
