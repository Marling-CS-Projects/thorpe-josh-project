# 2.2.12 Cycle 12 - Proper Levels & Enemy Spawning

## Design

### Objectives

In this cycle, my goal is to make some proper levels with enemies spawning in the correct places. My objectives in this cycle are to:

* [x] Make some proper level layouts
* [x] Change level spawning so that it pulls from 3 lists - 1 for each floor (this is so the enemy difficulty can increase over time
* [x] Enemies spawn in proper locations within the level
* [x] Text appears in the corner of your screen when entering a new floor saying the floor number
* [x] Text appears declaring when you have entered a boss fight
* [x] The player can advance through a door if all enemies have been killed
* [x] Make player and enemy bullets be able to destroy the boxes (mushrooms)

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

I created levels for each floor which are stored in separate files for ease of access.

<figure><img src="../.gitbook/assets/levels for floors.png" alt=""><figcaption><p>Files for possible levels of each floor</p></figcaption></figure>

Below are a couple of possible example levels from floor 2. As the floor increases, the levels become more difficult with more boxes and spikes and harder enemies spawn.

<pre class="language-typescript" data-title="possibleLevels2.ts"><code class="lang-typescript"><strong>export const possibleLevels2 = [
</strong>    {
        id: 11,
        enemy1Spawns: [],
        enemy2Spawns: [
            [397, 556],
            [1297, 343],
            [822, 273],
        ],
        enemy3Spawns: [
            [816, 580],
            [826, 111],
        ],
        enemy4Spawns: [],
        enemy5Spawns: [],
        enemy6Spawns: [],
        layout: [
            "====================",
            "= +    ^ +         =",
            "=  +            ^  =",
            "=   +     +        =",
            "= ^           +    =",
            "= +     +  ^       =",
            "=       ^     +    =",
            "= +   +         ^  =",
            "=@      +          #",
            "=                  =",
            "= +     ^      +   =",
            "=         +  ^ +   =",
            "= +           +  ++=",
            "=   ^              =",
            "=         ^^       =",
            "====================",
        ],
    },
    {
        id: 12,
        enemy1Spawns: [],
        enemy2Spawns: [
            [1152, 234],
        ],
        enemy3Spawns: [
            [1099, 94],
            [1189, 759],
        ],
        enemy4Spawns: [
            [405, 264],
        ],
        enemy5Spawns: [],
        enemy6Spawns: [],
        layout: [
            "====================",
            "=   +              =",
            "=  +  ^      +     =",
            "= + ^ +   ^        =",
            "=   +      +     + =",
            "=     + ^      ^   =",
            "=  +            +  =",
            "=     + +  +    +  =",
            "=@  +              #",
            "=                  =",
            "= + ^   +    ^  +  =",
            "=      +        +  =",
            "= +         +   +++=",
            "=   ^              =",
            "=         ^^       =",
            "====================",
        ],
    },
    
    ... //rest of the levels
]
</code></pre>

Below is the updated level selection code which now handles the three lists to draw for each floor. I moved it into a separate file since it got so long.

{% code title="level selection.ts" %}
```typescript
import { possibleLevels } from "./possibleLevels";
import { possibleLevels2 } from "./possibleLevels2";
import { possibleLevels3 } from "./possibleLevels3";
import { fixedLevels } from "./fixedLevels";


export function chooseLevels(chosenLevels) {
    chosenLevels.push({
        id: fixedLevels[0].id,
        layout: fixedLevels[0].layout,
        enemy1Spawns: fixedLevels[0].enemy1Spawns,
        enemy2Spawns: fixedLevels[0].enemy2Spawns,
        enemy3Spawns: fixedLevels[0].enemy3Spawns,
        enemy4Spawns: fixedLevels[0].enemy4Spawns,
        enemy5Spawns: fixedLevels[0].enemy5Spawns,
        enemy6Spawns: fixedLevels[0].enemy6Spawns,
    });
    for (let i = 0; i < 5; i++) {
        let randomIndex = Math.floor(Math.random() * possibleLevels.length);
        let randomLevel = {
            id: possibleLevels[randomIndex].id,
            layout: possibleLevels[randomIndex].layout,
            enemy1Spawns: possibleLevels[randomIndex].enemy1Spawns,
            enemy2Spawns: possibleLevels[randomIndex].enemy2Spawns,
            enemy3Spawns: possibleLevels[randomIndex].enemy3Spawns,
            enemy4Spawns: possibleLevels[randomIndex].enemy4Spawns,
            enemy5Spawns: possibleLevels[randomIndex].enemy5Spawns,
            enemy6Spawns: possibleLevels[randomIndex].enemy6Spawns,
        };
        chosenLevels.push(randomLevel);
    }
    chosenLevels.push({
        id: fixedLevels[1].id,
        layout: fixedLevels[1].layout,
        enemy1Spawns: fixedLevels[1].enemy1Spawns,
        enemy2Spawns: fixedLevels[1].enemy2Spawns,
        enemy3Spawns: fixedLevels[1].enemy3Spawns,
        enemy4Spawns: fixedLevels[1].enemy4Spawns,
        enemy5Spawns: fixedLevels[1].enemy5Spawns,
        enemy6Spawns: fixedLevels[1].enemy6Spawns,
    });
    chosenLevels.push({
        id: fixedLevels[0].id,
        layout: fixedLevels[0].layout,
        enemy1Spawns: fixedLevels[0].enemy1Spawns,
        enemy2Spawns: fixedLevels[0].enemy2Spawns,
        enemy3Spawns: fixedLevels[0].enemy3Spawns,
        enemy4Spawns: fixedLevels[0].enemy4Spawns,
        enemy5Spawns: fixedLevels[0].enemy5Spawns,
        enemy6Spawns: fixedLevels[0].enemy6Spawns,
    });
    for (let i = 0; i < 5; i++) {
        let randomIndex = Math.floor(Math.random() * possibleLevels2.length);
        let randomLevel = {
            id: possibleLevels2[randomIndex].id,
            layout: possibleLevels2[randomIndex].layout,
            enemy1Spawns: possibleLevels2[randomIndex].enemy1Spawns,
            enemy2Spawns: possibleLevels2[randomIndex].enemy2Spawns,
            enemy3Spawns: possibleLevels2[randomIndex].enemy3Spawns,
            enemy4Spawns: possibleLevels2[randomIndex].enemy4Spawns,
            enemy5Spawns: possibleLevels2[randomIndex].enemy5Spawns,
            enemy6Spawns: possibleLevels2[randomIndex].enemy6Spawns,
        };
        chosenLevels.push(randomLevel);
    }
    chosenLevels.push({
        id: fixedLevels[1].id,
        layout: fixedLevels[1].layout,
        enemy1Spawns: fixedLevels[1].enemy1Spawns,
        enemy2Spawns: fixedLevels[1].enemy2Spawns,
        enemy3Spawns: fixedLevels[1].enemy3Spawns,
        enemy4Spawns: fixedLevels[1].enemy4Spawns,
        enemy5Spawns: fixedLevels[1].enemy5Spawns,
        enemy6Spawns: fixedLevels[1].enemy6Spawns,
    });
    chosenLevels.push({
        id: fixedLevels[0].id,
        layout: fixedLevels[0].layout,
        enemy1Spawns: fixedLevels[0].enemy1Spawns,
        enemy2Spawns: fixedLevels[0].enemy2Spawns,
        enemy3Spawns: fixedLevels[0].enemy3Spawns,
        enemy4Spawns: fixedLevels[0].enemy4Spawns,
        enemy5Spawns: fixedLevels[0].enemy5Spawns,
        enemy6Spawns: fixedLevels[0].enemy6Spawns,
    });
    for (let i = 0; i < 5; i++) {
        let randomIndex = Math.floor(Math.random() * possibleLevels3.length);
        let randomLevel = {
            id: possibleLevels3[randomIndex].id,
            layout: possibleLevels3[randomIndex].layout,
            enemy1Spawns: possibleLevels3[randomIndex].enemy1Spawns,
            enemy2Spawns: possibleLevels3[randomIndex].enemy2Spawns,
            enemy3Spawns: possibleLevels3[randomIndex].enemy3Spawns,
            enemy4Spawns: possibleLevels3[randomIndex].enemy4Spawns,
            enemy5Spawns: possibleLevels3[randomIndex].enemy5Spawns,
            enemy6Spawns: possibleLevels3[randomIndex].enemy6Spawns,
        };
        chosenLevels.push(randomLevel);
    }
    chosenLevels.push({
        id: fixedLevels[1].id,
        layout: fixedLevels[1].layout,
        enemy1Spawns: fixedLevels[1].enemy1Spawns,
        enemy2Spawns: fixedLevels[1].enemy2Spawns,
        enemy3Spawns: fixedLevels[1].enemy3Spawns,
        enemy4Spawns: fixedLevels[1].enemy4Spawns,
        enemy5Spawns: fixedLevels[1].enemy5Spawns,
        enemy6Spawns: fixedLevels[1].enemy6Spawns,
    });
};
```
{% endcode %}

Here is the code within the level scene which is responsible for the boss and floor text which appears. The text entities are created with their `opacity` set to 0 and when the desired level appears, the `opacity` is set to 1 for a short duration which reveals the text before it is hidden again.

{% code title="main.ts" %}
```typescript
const bossFightText = add([
        text("Boss Fight", {
            size: 70,
        }),
        pos(1550, 60),
        area({ cursor: "pointer" }),
        z(10),
        opacity(0),
        "bossFightText",
    ]);

    if (chosenLevelIndex === 6 || chosenLevelIndex === 13 || chosenLevelIndex === 20) {
        bossFightText.opacity = 1;
        wait(2, () => {
            bossFightText.opacity = 0;
        });
    };

        const newFloorText = add([
        text("Floor " + floorNumber, {
        size: 70,
        }),
        pos(1550, 60),
        z(10),
        opacity(0),
        "newFloorText",
    ]);
    
    if (chosenLevelIndex === 0) {
        floorNumber = 1;
        newFloorText.text = "Floor " + floorNumber,
        newFloorText.opacity = 1;
        wait(2, () => {
            newFloorText.opacity = 0;
        });
    } else if (chosenLevelIndex === 7) {
        floorNumber = 2;
        newFloorText.text = "Floor " + floorNumber,
        newFloorText.opacity = 1;
        wait(2, () => {
            newFloorText.opacity = 0;
        });
    } else if (chosenLevelIndex === 14) {
        floorNumber = 3;
        newFloorText.text = "Floor " + floorNumber,
        newFloorText.opacity = 1;
        wait(2, () => {
            newFloorText.opacity = 0;
        });
    };
```
{% endcode %}

When a `player_bullet` or `enemy_bullet` collides with a `box` then both the box and the bullet are destroyed.

{% code title="main.ts" %}
```typescript
//box collision
    onCollide("player_bullet", "box", (bullet, box) => {
        destroy(bullet); // Destroy the bullet
        destroy(box);   // Destroy the box
    });

    onCollide("enemy_bullet", "box", (bullet, box) => {
        destroy(bullet); // Destroy the bullet
        destroy(box);   // Destroy the box
    });
```
{% endcode %}



<pre class="language-typescript" data-title="main.ts"><code class="lang-typescript"><strong>    "#": () => [
</strong>          sprite("door"),
          area(),
          anchor("center"),
          z(2),
          body(),
          "door",
      ],
                                               
<strong> onCollide("player", "door", (bullet, box) => {
</strong>        enemiesRemaining = get("enemy").length;
        if (enemiesRemaining === 0) {
            chosenLevelIndex += 1;
            destroyAll("entity");
            if (chosenLevelIndex > chosenLevels.length - 1) {
                go("win");
            } else {
                go("level", chosenLevelIndex);
            }
        };
    });
</code></pre>

### Challenges

At first, I did not know which way around visible and invisible were for opacity.

## Testing

### Tests

| Test | Instructions   | What I expect      | What actually happens | Pass/Fail |
| ---- | -------------- | ------------------ | --------------------- | --------- |
| 1    | Run code.      | Thing happens.     | As expected.          | Pass.     |
| 2    | Press buttons. | Something happens. | As expected.          | Pass.     |

### Evidence

{% embed url="https://youtu.be/8OWUxVaDSTw" %}
