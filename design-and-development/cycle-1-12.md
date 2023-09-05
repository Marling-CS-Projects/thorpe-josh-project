# 2.2.11 Cycle 11 - Main Menu & Character Select

## Design

### Objectives

In this cycle, I will. My objectives in this cycle are:

* [x] Add shopkeeper sprite
* [x] Fix enemy shooting once after death bug
* [x] When the game starts you are greeted with a main menu
* [x] It has 'play', 'how to play' and 'credits'
* [x] 'play' takes you to character select where choose your character
* [x] you can choose from 3 characters which each have different stats and starting weapons
* [x] 'how to play' gives some basic instructions
* [x] 'credits' gives sprite, sound effect and music credits
* [x] health bar starting width scales with the health of character

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
scene("mainMenu", () => {
    const playButton = add([
        text("Play", {
            size: 40,
        }),
        pos(width() / 2, height() / 2 - 40),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    const howToPlayButton = add([
        text("How to Play", {
            size: 40,
        }),
        pos(width() / 2, height() / 2 + 20),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    const creditsButton = add([
        text("Credits", {
            size: 40,
        }),
        pos(width() / 2, height() / 2 + 80),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    playButton.onClick(() => {
        go("characterSelect");
    });

    howToPlayButton.onClick(() => {
        go("howToPlay");
    });

    creditsButton.onClick(() => {
        go("credits");
    });
});

// How to Play Scene
scene("howToPlay", () => {
    const backButton = add([
        text("Back to Main Menu", {
            size: 20,
        }),
        pos(width() / 2, height() - 30),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    const howToPlayText = add([
        text("Instructions:\n\nWASD to move\nLeft click to shoot\nRight click to dash\nNumber keys to switch weapons\nClear all the enemies in a room to advance\nEscape the dungeon!",
        {
            size: 24,
            width: width() - 40,
            align: "center",
        }),
        pos(width() / 2, height() / 2),
        anchor("center"),
        z(10),
    ]);

    backButton.onClick(() => {
        go("mainMenu");
    });
});

// Credits Scene
scene("credits", () => {
    const backButton = add([
        text("Back to Main Menu", {
            size: 20,
        }),
        pos(width() / 2, height() - 30),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    const creditsText = add([
        text("Programming by Josh Thorpe\nMusic by none\nSprites by Kaboom",
        {
            size: 24,
            width: width() - 40,
            align: "center",
        }),
        pos(width() / 2, height() / 2),
        anchor("center"),
        z(10),
    ]);

    backButton.onClick(() => {
        go("mainMenu");
    });
});

scene("characterSelect", () => {
    const characterData = [
        {
            name: "Scarlett Blackthorn",
            sprite: "apple",
            health: 80,
            speed: 300,
            dashLength: 1,
            dashCooldown: 2,
        },
        {
            name: "Sir Galahad",
            sprite: "egg",
            health: 200,
            speed: 175,
            dashLength: 0.3,
            dashCooldown: 3,
        },
        {
            name: "Deadeye Dave",
            sprite: "bean",
            health: 100,
            speed: 225,
            dashLength: 0.5,
            dashCooldown: 1,
        },
    ];

    const characterSpacing = 310;
    const characterY = height() / 2;


     const whiteOutline = add([
        rect(400, 600),// Rectangle size and dimensions
        rgb(255, 255, 255), // Transparent white
        z(10),
        pos((characterSpacing * 2) - 60, characterY), // Initial position (off-screen)
        anchor("center"),
        z(8), // Behind the sprites
        opacity(0),
    ]);

    characterData.forEach((character, index) => {
        const characterName = add([
            text(character.name, {
                size: 40,
            }),
            pos((index + 2) * characterSpacing, characterY - 150),
            anchor("center"),
            z(10),
            color(BLACK),
        ]);

        const characterSprite = add([
            sprite(character.sprite),
            pos((index + 2) * characterSpacing, characterY),
            anchor("center"),
            area({ cursor: "pointer" }),
            z(10),
            "character",
            {
                health: character.health,
                speed: character.speed,
            },
            scale(3),
        ]);

        characterSprite.onClick(() => {
            playerHP = characterSprite.health;
            ORIGINALHP = playerHP;
            playerSpeed = characterSprite.speed;
            whiteOutline.opacity = 1;
            whiteOutline.pos = characterSprite.pos;
            selectedCharacterSprite = character.sprite;
        });
    });

    const playButton = add([
        text("Play", {
            size: 40,
        }),
        pos(width() / 2, height() - 90),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    playButton.onClick(() => {
        let chosenLevelIndex = 0;
        go("level", chosenLevelIndex);
    });

    const backButton = add([
        text("Back to Main Menu", {
            size: 20,
        }),
        pos(width() / 2, height() - 30),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    backButton.onClick(() => {
        go("mainMenu");
    });
});


go("mainMenu");
```

explain some more

```typescript
 "@": () => [
                sprite(selectedCharacterSprite),
                area(),
                anchor("center"),
                z(2),
                body(),
                health(playerHP),
                "player",
            ],
```

I added an isAlive flag in the enemy class which is used to check if the enemy is still alive before it spawns a bullet.

### Challenges

I found using variables to pass information between scenes challenging because the variables must be defined outside the scenes to make it a global variable.

## Testing

### Tests

| Test | Instructions                                               | What I expect                                                 | What actually happens | Pass/Fail |
| ---- | ---------------------------------------------------------- | ------------------------------------------------------------- | --------------------- | --------- |
| 1    | Start game.                                                | Start on the main menu.                                       | As expected.          | Pass.     |
| 2    | Click each button and then the return to main menu button. | Page opens each time before returning to main menu each time. | As expected.          | Pass.     |
| 3    | Open character selection menu and click on each sprite.    | White selection box moves to each one expected.               | As expected.          | Pass.     |
| 4    | Click play for each charater.                              | Health bar is different for each character.                   | As expected.          | Pass.     |
| 5    | Use 'r' key to cycle through levels.                       | For the designated shop levels there is a shopkeeper sprite.  | As expected.          | Pass.     |

During testing, I noticed an instance of a bullet spawning from a deleted enemy when moving between levels. However, I've managed to reduce cases of this greatly. I'll see if it noticeably continues and if it does then I will fix it in an upcoming cycle.

### Evidence

{% embed url="https://youtu.be/ecOnOjPC7qA" %}
