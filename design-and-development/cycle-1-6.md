# 2.2.7 Cycle 7 - Improved Room Generation

## Design

### Objectives

My main objective in this cycle is to alter the level system within the game so that:

* [x] Each floor starts with a shop room (empty for now)
* [x] 5 random levels are chosen from the `possibleLevels` array and these are played next
* [x] A floor ends with a boss room (empty for now)

#### Smaller Changes

I also want to add a cooldown to the gun so that the player can't just click their mouse really fast to kill all of the enemies.

* [x] Add a gun cooldown which limits how fast the gun can shoot

### Key Variables

| Variable Name     | Use                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `fixedLevels`     | An array of fixed level configurations, where each level is represented as an array of strings. It is imported from the `fixedLevels.js` module.                                                                                                                                                                                                                                                                                              |
| `chosenLevels`    | An array that stores the selected levels for gameplay. It initially starts as an empty array and is populated with level configurations based on the `fixedLevels` array and randomly chosen levels from `possibleLevels` (which is not defined in the provided code snippet). The structure of `chosenLevels` is `[shop level, random level 1, random level 2, ..., random level 5, boss level]`, and this sequence is repeated three times. |
| `gunCooldownTime` | A variable that represents the cooldown time in seconds for the player's gun. It is set to 2 seconds.                                                                                                                                                                                                                                                                                                                                         |
| `gunCooldown`     | A boolean variable that keeps track of whether the gun is on cooldown or not. It is initially set to `false` and is used to prevent the player from firing the gun during the cooldown period.                                                                                                                                                                                                                                                |

### Pseudocode

```
// Import necessary modules
import { fixedLevels } from "./fixedLevels";

// Define key variables
let chosenLevels = []
let levelId = 0
let gunCooldownTime = 2;
let gunCooldown = false;

// Function to handle level completion
function handleLevelCompletion() {
    if (levelId > chosenLevels.length - 1) {
        go("win");
    } else {
        go("level", levelId);
    }
}

// Function to handle gun cooldown
function handleGunCooldown() {
    gunCooldown = true;
    spawnBullet(player.pos.add(vec2(350, 50)));
    wait(gunCooldownTime, () => {
        gunCooldown = false;
    });
}

// Event handlers
onKeyPress("r", () => {
    handleLevelCompletion();
});

onMousePress("left", () => {
    if (!gunCooldown) {
        handleGunCooldown();
    }
});

// Main code execution
for (let j = 0; j < 3; j++) {
    chosenLevels.push(fixedLevels[0]);
    for (let i = 0; i < 5; i++) {
        let randomIndex = Math.floor(Math.random() * possibleLevels.length);
        let randomLevel = possibleLevels[randomIndex];
        chosenLevels.push(randomLevel);
    }
    chosenLevels.push(fixedLevels[1]);
}

const level = addLevel(chosenLevels[levelId], {...});

```

## Development

### Outcome

I made a new file called `./fixedLevels` to hold the shop and boss levels.

{% code title="fixedLevels.js" %}
```javascript
export const fixedLevels = [
    [
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
    [
    "====================",
    "=   @              =",
    "=                  =",
    "=                  =",
    "=                  =",
    "=                  =",
    "=                  =",
    "=                  =",
    "=                  =",
    "=                  =",
    "=                  =",
    "=                  =",
    "=                  =",
    "=                  =",
    "=                  =",
    "====================",
    ],
];

// 1st level shop level
// 2nd level boss level
```
{% endcode %}

3 floors are created, each with a shop room, 5 random levels, and then a boss room. They are all combined into one array called `chosenLevels`.

{% code title="main.js" %}
```javascript
import { fixedLevels } from "./fixedLevels";

let chosenLevels = [] // Array to store selected levels
// Loop to generate chosenLevels array
for (let j = 0; j < 3; j++) {
    chosenLevels.push(fixedLevels[0]); // Add shop level
    for (let i = 0; i < 5; i++) {
        let randomIndex = Math.floor(Math.random() * possibleLevels.length); // Randomly select a level index
        let randomLevel = possibleLevels[randomIndex]; // Get the randomly selected level
        chosenLevels.push(randomLevel); // Add the random level to chosenLevels
    }

    chosenLevels.push(fixedLevels[1]); // Add boss level
}
```
{% endcode %}

Both `addLevel()` and the check for the last level were updated to use the `chosenLevels` array.

{% code title="main.js" %}
```javascript
// Get the current level configuration
const level = addLevel(chosenLevels[levelId], {...})

onKeyPress("r", () => {
    ... // Other code
     // Check if levelId exceeds the chosenLevels array length
    if (levelId > chosenLevels.length - 1) {
        go("win"); // Go to the "win" scene if all levels are completed
    } else {
        go("level", levelId); // Go to the next level scene
    }
});
```
{% endcode %}

I modified how the `addBullet()` function is called so that it can only be called every 2 seconds. I've used a variable for the cooldown time so that it can be easily modified in the future when I add different weapons which have different statistics.

{% code title="main.js" %}
```javascript
let gunCooldownTime = 2; // Cooldown time for the gun (seconds)
let gunCooldown = false; // Flag to track gun cooldown status

onMousePress("left", () => {
    // Check if the gun is not on cooldown
    if (!gunCooldown) {
        gunCooldown = true; // Set the gun cooldown flag
        spawnBullet(player.pos.add(vec2(350, 50))); // Spawn a bullet at the player's position
        wait(gunCooldownTime, () => {
            gunCooldown = false; // Reset the gun cooldown flag after the specified cooldown time
        });
    }
});
```
{% endcode %}

### Challenges

Initially, I had the boss level and shop level stored in separate files as an array with 1 element which was a list of the tiles. However, I soon found it was much simpler to store them together in a single array to avoid confusion. I also had trouble deleting the old files that they were in (`shopLevel.js` and `bossLevel.js`) because whenever I deleted them they would just instantly reappear.

## Testing

### Tests

| Test | Instructions                                                                        | What I expect                                                                | What actually happens | Pass/Fail |
| ---- | ----------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- | --------------------- | --------- |
| 1    | Run code & start levels.                                                            | Empty level with player in the middle.                                       | As expected.          | Pass.     |
| 2    | Press 'R' 5 times.                                                                  | Game cycles through 5 random levels.                                         | As expected.          | Pass.     |
| 3    | Press 'R' again.                                                                    | Empty level with player towards the top left.                                | As expected.          | Pass.     |
| 4    | Press 'R' again.                                                                    | Empty level with player in the middle.                                       | As expected.          | Pass.     |
| 5    | Repeat tests 2  - 4 twice more.                                                     | Same results as in tests 2 - 4, but with different random levels for test 2. | As expected.          | Pass.     |
| 6    | Press 'R' again.                                                                    | Game goes to end screen (empty screen).                                      | As expected.          | Pass.     |
| 7    | Attempt to shoot the gun faster than the cooldown by clicking very fast repeatedly. | A bullet only spawns every 2 seconds.                                        | As expected.          | Pass.     |

### Evidence

{% embed url="https://youtu.be/KihXnebhu_8" %}
