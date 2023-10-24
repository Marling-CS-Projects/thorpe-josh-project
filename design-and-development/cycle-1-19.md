# 2.2.19 Cycle 19 - Balancing & Removing Test Features

## Design

### Objectives

In this cycle, my goal is to make the game ready for players by removing the testing features and balancing the game as best as possible so that it is fun to play. My objectives in this cycle are to:

* [x] Balance the weapons, enemies, bosses and levels accordingly so that the game feels fun to play and fair
  * [x] Increase the number of pellets from the Boomstick
* [x] Ensure that no enemies spawn too close to the player
* [x] Remove the test features I added for development purposes

#### Smaller Changes

* [x] Change the shop text so that the weapons are in the order of the keys pressed to unlock
* [x] Add the mass property to each entity to change how easily different enemies can push each other around (I want the bosses to feel heavier)

### Pseudocode

```
Create a player entity with the following components:
- Sprite using selectedCharacterSprite
- Area
- Anchor at the center
- Z-index of 4
- Physics body with a mass of 3
- Health component with initial health playerHP
- Tag "player"
```

## Development

### Outcome

I changed many of the statistics for the characters, weapons and enemies and changed some of the level spawning so that the game feels much more balanced and fair to play.

Example use of the mass property when creating the player.

```typescript
"@": () => [
                sprite(selectedCharacterSprite),
                area(),
                anchor("center"),
                z(4),
                body({mass: 3}), // Mass of 3
                health(playerHP),
                "player",
            ],
```

I commented out the code for adding coins and cycling levels and removed the colour of enemies changing when they are about to shoot as these were features I only intended to have during development.

The final version of my game can be found here: [https://replit.com/@JoshuaThorpe/A-level-Project-v10](https://replit.com/@JoshuaThorpe/A-level-Project-v10).

### Challenges

I did the best job I could at balancing the game however it will be hard to know if the game is properly balanced until it is played extensively and I get user feedback.

## Testing

### Tests

| Test | Instructions                                                 | What I expect                                                                                             | What actually happens                               | Pass/Fail |
| ---- | ------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | --------------------------------------------------- | --------- |
| 1    | Play the game.                                               | <ul><li>No enemies spawn too close to the player.</li><li>Enemies no longer flash blue and red.</li></ul> | <ul><li>As expected.</li><li>As expected.</li></ul> | Pass.     |
| 2    | Try to use the development features by pressing 'M' and 'R'. | Nothing happens.                                                                                          | As expected.                                        | Pass.     |

Now that the final cycle of my game has been completed I will begin overall testing in Section 3. This will include [testing for function and robustness](../testing/3.1-robustness.md), [usability testing](../testing/3.2-usability-testing.md), and [checking development tests](../testing/3.3-checking-development-tests.md).

### Evidence

{% embed url="https://youtu.be/fKQJHg7ysso" %}
