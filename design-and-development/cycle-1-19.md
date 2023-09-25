# 2.2.19 Cycle 19 - Balancing & Removing Test Features

## Design

### Objectives

In this cycle, my goal is to make the game ready for players by removing the testing features and balancing the game as best as possible so that it is fun to play. My objectives in this cycle are to:

* [x] Balance the weapons, enemies, bosses and levels accordingly so that the game feels fun to play and fair
* [x] Ensure that no enemies spawn too close to the player
* [x] Remove the test features I added for development purposes

#### Smaller Changes

* [x] Increase the number of pellets from the Boomstick
* [x] Change key unlocks in shop so that they are in the order of the keyboard
* [x] Add the mass property to each entity to dictate how easily they are pushed around

### Usability Features

Add this section

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

I changed the statistics of the characters, weapons and enemies and changed some of the level spawning so that the game feels much more balanced.

Example of the mass property for the player entity.

```typescript
"@": () => [
                sprite(selectedCharacterSprite),
                area(),
                anchor("center"),
                z(4),
                body({mass: 3}),
                health(playerHP),
                "player",
            ],
```

I commented out the code for adding coins and cycling levels and removed the colour of enemies changing when they are about to shoot.

### Challenges

I did the best job I could at balancing the game however it will be hard to know if the game is properly balanced until it is played extensively and I get user feedback.

## Testing

### Tests

| Test | Instructions                                                 | What I expect                                                                                             | What actually happens                               | Pass/Fail |
| ---- | ------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | --------------------------------------------------- | --------- |
| 1    | Play game.                                                   | <ul><li>No enemies spawn too close to the player.</li><li>Enemies no longer flash blue and red.</li></ul> | <ul><li>As expected.</li><li>As expected.</li></ul> | Pass.     |
| 2    | Try to use the development features by pressing 'M' and 'R'. | Nothing happens.                                                                                          | As expected.                                        | Pass.     |

Now that the final cycle of my game has been completed I will begin overall testing in Section 3. This will include [testing for function and robustness](../testing/3.1-robustness.md), [usability testing](../testing/3.2-usability-testing.md), and [checking development tests](../testing/3.3-checking-development-tests.md).

### Evidence

Put YouTube video testing link here
