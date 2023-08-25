# 2.2.9 Cycle 9 - Re-adding Content & Bullet Damage

## Design

### Objectives

My objectives in this cycle are:

* [x] Re-add player health system and spikes
* [x] Re-add health bar
* [x] Re-add background colour
* [x] Enemy bullets can damage the player
* [x] Different enemy types deal different amounts of damage
* [x] Make dash time shorter but with a shorter cooldown
* [ ] Add coin system where killing an enemy gives +1 coin

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

I added `shootDamage` to the contructor and it is set to a different value for each enemy type. This value is then added to the bullets spawned by enemies.

```typescript
    shootProjectile(targetPos: Vec2) {
        const projectileSpeed = 500; // Adjust as needed
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
            move(direction, projectileSpeed),
            "enemy_bullet",
            { shootDamage: this.shootDamage },
            offscreen({ destroy: true }),
        ]);
    }
```

When an enemy bullet and the player collide, the player will be damaged by the value of shootDamage.

```typescript
    onCollide("enemy_bullet", "player", (bullet, player) => {
        destroy(bullet);
        player.hurt(bullet.shootDamage);
        playerHP = player.hp();
        updateHealthBar();
    });
```

I re-added spikes and the health bar, content from [Cycle 5](cycle-1-5.md).

### Challenges

I found adding `shootDamage` a bit difficult at first, however once I found out how to do it, it was quite simple.

## Testing

### Tests

| Test | Instructions  | What I expect     | What actually happens | Pass/Fail |
| ---- | ------------- | ----------------- | --------------------- | --------- |
| 1    | Run code      | Thing happens     | As expected.          | Pass.     |
| 2    | Press buttons | Something happens | As expected.          | Pass.     |

comment on any failed tests and how you plan to resolve them

### Evidence

put youtube video testing link here
