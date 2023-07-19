# 2.2.8.2 Cycle 8b - Overhauling Enemy System & Adding Attacks

## Design

### Objectives

My objectives in this cycle are to completely redo enemies in a better way:

* [ ] Overhaul the enemy system using an object-oriented approach
* [ ] Have 3 enemy types which can be spawned
* [ ] Each enemy type has different statistics
* [ ] Add enemy projectile attacks
* [ ] Enemy projectiles can damage the player

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

```javascript
// Some code
```

explain some more

```javascript
// Some more code
```

### Challenges

Initially implementing enemy attacks I experienced a bug where after an enemy died, bullets would continue to fire at the player from the position the enemy died at.

{% embed url="https://youtu.be/Makqcr3obds" %}

Then I was able to make it so that after an enemy died, the remaining enemies stopped firing too. After struggling with this for a while, I concluded that my approach was too challenging to make work correctly. Therefore I decided to convert my project to an object-oriented approach.

## Testing

### Tests

| Test | Instructions  | What I expect     | What actually happens | Pass/Fail |
| ---- | ------------- | ----------------- | --------------------- | --------- |
| 1    | Run code      | Thing happens     | As expected           | Pass      |
| 2    | Press buttons | Something happens | As expected           | Pass      |

comment on any failed tests and how you plan to resolve them

### Evidence

put youtube video testing link here
