# 2.2.8.1 Cycle 8a - Enemy Attacks

## Design

### Objectives

My objectives in this cycle are to:

* [x] Add enemy projectile attacks
* [ ] Enemy projectiles can damage the player
* [ ] Add 3 more enemy types which can be put in levels
* [ ] Enemy types have different movements, attacks, damage and health
* [ ] Make enemy and player bullets able to destroy boxes (which are currently mushrooms)

## Development

explain the code

```javascript
// Some code
```

explain some more

```javascript
// Some more code
```

### Challenges

While implementing enemy attacks I experienced a bug where after an enemy died, bullets would continue to fire at the player from the position the enemy died at.

{% embed url="https://youtu.be/Makqcr3obds" %}

Then I was able to make it so that after an enemy died, the remaining enemies stopped firing too.&#x20;

I've now concluded that my approach was is challenging to make work correctly. Therefore I've decided to rework my approach to enemies completely, using an object-oriented approach.&#x20;

## Testing

During development, I quickly realised that my approach was buggy and confusing, so I decided that approaching the cycle in this way was not a good idea. See [Cycle 8b](cycle-1-9.md) for the restart of Cycle 8.
