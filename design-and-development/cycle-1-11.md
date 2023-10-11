# 2.2.11 Cycle 11 - Main Menu & Character Select

## Design

### Objectives

In this cycle, my objective is to create a main menu at the start of the game with character selection. My objectives in this cycle are:

* [x] When the game starts you are greeted with a main menu
* [x] There are buttons for 'play', 'how to play', and 'credits' which each take you to a separate menu
* [x] 'how to play' gives some basic instructions
* [x] 'credits' gives sprite, sound effects and music credits
* [x] 'play' goes to the character select menu
* [x] You can choose from 3 characters, each with different statistics
* [x] Click on the character and then click another play button to start the game
* [x] When a character is clicked on, there is a white selection box behind it
* [x] The selection box moves behind the next one which is clicked
* [x] Health bar starting width scales with the health of the character

#### Smaller Changes

* [x] Add shopkeeper sprite in the shop levels
* [x] Fix enemies shooting once after death bug

### Usability Features

The how to play menu will help players learn how to play the game. The selection box in the character select menu shows players which character they are currently set to play as.

### Key Variables

| Variable Name                                                      | Use                                                                                                                                                                                                         |
| ------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `playButton`, `howToPlayButton`, `creditsButton`                   | These variables represent buttons displayed in different scenes. They are used to handle click events to transition between scenes.                                                                         |
| `howToPlayText`, `creditsText`                                     | These variables represent text elements displaying instructions and credits. They are used to display information in the `howToPlay` and `credits` scenes.                                                  |
| `characterData`                                                    | This variable stores an array of objects, each representing character information (e.g., name, sprite, health, speed).                                                                                      |
| `characterSpacing`, `characterY`                                   | These variables define the spacing and vertical position of character elements in the `characterSelect` scene.                                                                                              |
| `whiteOutline`                                                     | This variable represents a white outline element that appears when a character is selected. It is used to visually highlight the selected character.                                                        |
| `characterName`, `characterSprite`                                 | These variables represent text and sprite elements for each character in the `characterSelect` scene. They are used to display character names and sprites and handle click events for character selection. |
| `playerHP`, `playerSpeed`, `ORIGINALHP`, `selectedCharacterSprite` | These variables are used to store and manage player-related data, such as health, speed, and the selected character's sprite. They are modified when a character is selected and used in the `level` scene. |
| `playButton` (in the `characterSelect` scene)                      | This variable represents a button to start the game. It handles the click event to transition to the `leve`l scene with the selected character.                                                             |
| `backButton`                                                       | This variable represents a button to return to the main menu. It handles the click event to transition back to the main menu from different scenes.                                                         |

### Pseudocode

```
# Main Menu Scene
scene("mainMenu"):
    create playButton
    create howToPlayButton
    create creditsButton

    playButton.onClick():
        transition to "characterSelect" scene

    howToPlayButton.onClick():
        transition to "howToPlay" scene

    creditsButton.onClick():
        transition to "credits" scene

# How to Play Scene
scene("howToPlay"):
    create backButton
    create howToPlayText

    backButton.onClick():
        transition to "mainMenu" scene

# Credits Scene
scene("credits"):
    create backButton
    create creditsText

    backButton.onClick():
        transition to "mainMenu" scene

# Character Selection Scene
scene("characterSelect"):
    create characterData

    for each character in characterData:
        create characterName
        create characterSprite

        characterSprite.onClick():
            set playerHP to character's health
            set ORIGINALHP to playerHP
            set playerSpeed to character's speed
            show whiteOutline around selected character

    create playButton
    create backButton

    playButton.onClick():
        set chosenLevelIndex to 0
        transition to "level" scene with chosenLevelIndex

    backButton.onClick():
        transition to "mainMenu" scene

# Level Scene
scene("level", chosenLevelIndex):
    # Game logic for the actual gameplay goes here

# Initialize the game by starting at the Main Menu
go("mainMenu")
```

## Development

### Outcome

Each screen of the main menu is contained within a separate scene. To move between pages of the menu, the game moves between scenes.

```typescript
// Main Menu scene
scene("mainMenu", () => {

    // Create "Play" button
    const playButton = add([
        text("Play", {
            size: 40,
        }),
        pos(width() / 2, height() / 2 - 40),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    // Create "How to Play" button
    const howToPlayButton = add([
        text("How to Play", {
            size: 40,
        }),
        pos(width() / 2, height() / 2 + 20),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    // Create "Credits" button
    const creditsButton = add([
        text("Credits", {
            size: 40,
        }),
        pos(width() / 2, height() / 2 + 80),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    // Handle Play button click
    playButton.onClick(() => {
        go("characterSelect"); // Transition to "Character Select" scene
    });

    // Handle How to Play button click
    howToPlayButton.onClick(() => {
        go("howToPlay"); // Transition to "How to Play" scene
    });

    // Handle Credits button click
    creditsButton.onClick(() => {
        go("credits"); // Transition to "Credits" scene
    });
});

```

How to play page of the menu. It contains some text which describes how to play and a button which takes you back to the main menu.

```typescript
// How to Play scene
scene("howToPlay", () => {

    // Create Back to Main Menu button
    const backButton = add([
        text("Back to Main Menu", {
            size: 20,
        }),
        pos(width() / 2, height() - 30),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    // Create text entity explaining how to play
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

    // Handle Back to Main Menu button click
    backButton.onClick(() => {
        go("mainMenu"); // Transition back to Main Menu
    });
});
```

Credits page of the menu. It contains some text for the credits and a button which takes you back to the main menu.

```typescript
// Credits Scene
scene("credits", () => {
    // Create Back to Main Menu button
    const backButton = add([
        text("Back to Main Menu", {
            size: 20,
        }),
        pos(width() / 2, height() - 30),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    // Create a text entity for the credits
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

    // Handle Back to Main Menu button click
    backButton.onClick(() => {
        go("mainMenu"); // Transition back to Main Menu
    });
});
```

Character selection page of the menu. The statistics of each character are stored in `characterData` and the player's stats are set to those of the character when each sprite is clicked. The play button starts the game with the player's current stats while the back button takes you back to the main menu.

```typescript
// Character Selection scene
scene("characterSelect", () => {

    // Define character data
    const characterData = [
        {
            name: "Scarlett Blackthorn",
            sprite: "apple", // These are all the stats which vary for each character
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

    // Spacing of the character sprites on the selection screen
    const characterSpacing = 310;
    const characterY = height() / 2;

    // Create a white box (hidden by default)
    const whiteOutline = add([
        rect(400, 600), // Rectangle size and dimensions
        rgb(255, 255, 255), // White
        z(10),
        pos(0, characterY), // Initial position
        anchor("center"),
        z(8), // Layer which is behind the sprites
        opacity(0), // Hidden by default
    ]);

    // Iterate through character data to create character selection elements
    characterData.forEach((character, index) => {
        // Create character name text
        const characterName = add([
            text(character.name, {
                size: 40,
            }),
            pos((index + 2) * characterSpacing, characterY - 150),
            anchor("center"),
            z(10),
            color(BLACK),
        ]);

        // Create character sprite
        const characterSprite = add([
            sprite(character.sprite),
            pos((index + 2) * characterSpacing, characterY),
            anchor("center"),
            area({ cursor: "pointer" }), // Make it clickable
            z(10),
            "character", // Tag as a character
            {
                health: character.health,
                speed: character.speed,
            },
            scale(3),
        ]);

        // Handle character selection
        characterSprite.onClick(() => {
            playerHP = characterSprite.health;
            ORIGINALHP = playerHP;
            playerSpeed = characterSprite.speed;
            whiteOutline.pos = characterSprite.pos; // White outline moves to selected character
            whiteOutline.opacity = 1; // Show white outline
            selectedCharacterSprite = character.sprite;
        });
    });

    // Create Play Game button
    const playButton = add([
        text("Play", {
            size: 40,
        }),
        pos(width() / 2, height() - 90),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    // Handle Play Game button click
    playButton.onClick(() => {
        let chosenLevelIndex = 0;
        go("level", chosenLevelIndex); // Transition to the level scene with the selected character
    });

    // Create Back to Main Menu button
    const backButton = add([
        text("Back to Main Menu", {
            size: 20,
        }),
        pos(width() / 2, height() - 30),
        anchor("center"),
        area({ cursor: "pointer" }),
        z(10),
    ]);

    // Handle Back to Main Menu button click
    backButton.onClick(() => {
        go("mainMenu"); // Transition back to Main Menu
    });
});
```

The game starts in the main menu scene.

```typescript
go("mainMenu");
```

The sprite of the player now depends on the character which was selected instead of being constant each time.

```typescript
 "@": () => [ // @ represents the player in the tile maps
                sprite(selectedCharacterSprite),
                area(),
                anchor("center"),
                z(2),
                body(),
                health(playerHP),
                "player",
            ],
```

I have also added an `isAlive` flag in the enemy class which is used to check if the enemy is still alive before it spawns a bullet. This is to eliminate the bug where an enemy may still shoot its projectile shortly after dying.

### Challenges

To use a variable between different scenes it must be declared globally at the start of the project. It took me a little while to figure this out which made it a bit challenging when making the character selection system.

## Testing

### Tests

| Test | Instructions                                               | What I expect                                                                                                                     | What actually happens                               | Pass/Fail |
| ---- | ---------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- | --------- |
| 1    | Start game.                                                | Game starts on the main menu.                                                                                                     | As expected.                                        | Pass.     |
| 2    | Click each button and then the return to main menu button. | Page opens each time before returning to main menu each time when the back button is clicked.                                     | As expected.                                        | Pass.     |
| 3    | Open character selection menu and click on each sprite.    | White selection box moves to each one expected.                                                                                   | As expected.                                        | Pass.     |
| 4    | Click play for each character.                             | <ol><li>Health bar is different for each character.</li></ol><ol start="2"><li>Each character has different move speed.</li></ol> | <ol><li>As expected.</li><li>As expected.</li></ol> | Pass.     |
| 5    | Use r key to cycle through levels.                         | When there is a shop level there is a shopkeeper sprite in it.                                                                    | As expected.                                        | Pass.     |

During testing, I noticed an instance of a bullet spawning from a deleted enemy when moving between levels. This can be seen in the evidence video below. I was surprised by this since I thought that the bug would be completely fixed with the `isAlive` flag I added to each enemy. It may have occurred due to a brief lag spike because I could not replicate it. If it happens again or more frequently, then I will address it in a later cycle.

### Evidence

{% embed url="https://youtu.be/ecOnOjPC7qA" %}
