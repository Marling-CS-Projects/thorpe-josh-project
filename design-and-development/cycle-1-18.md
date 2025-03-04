# 2.2.18 Cycle 18 - Sound & Improved Menus

## Design

### Objectives

In this cycle, I plan to add music and sound effects to my game which provide feedback to the player after key events. The music should also make the game feel more exciting. My objectives in this cycle are to:

* [x] Add sound effects for key events
* [x] Add music which loops throughout the game
* [x] Update the credits menu to include credits for the music and sound effects
* [x] Music becomes quieter when the game is paused
* [x] Music pauses in the win and lose scenes

My secondary objective in this cycle is to improve the menu screens. I am still unhappy with the main menu when you enter the game because the buttons do not feel properly clickable and the whole menu seems a bit bland which isn't very fun for the player. This is also bad because the menu is the first thing the player sees when playing my game, so it shouldn't put them off.  My secondary objectives in this cycle are to:

* [x] Make the menu buttons look like actual buttons
* [x] Remove the object which creates a separate menu colour and change the project background colour
* [x] Replace the how to play menu text with a new visual graphic I made
* [x] Add an exciting font for the titles of each menu

#### Smaller Changes

* [x] Player and enemy bullets are destroyed when colliding with the door
* [x] Increase boss reward from 1 to 5 coins
* [x] Add a new enemy 3 sprite
* [x] Add a crown to the final boss sprite
* [x] Add a new shopkeeper sprite

### Usability Features

The main menu and character selection allow users to easily navigate through my game with ease and enjoyment. The colours are specifically chosen to be clear and easy to use.

### Key Variables

| Variable Name | Use                                                                                                                                                                                                                 |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `gameMusic`   | This variable is used to reference the game's background music sound. It is created using the `play` function and set to loop indefinitely. This variable allows me to control the music by stopping or pausing it. |

### Pseudocode

```
// Define and load game sounds
loadSound("menuclick", "/sounds/select-sound-121244.mp3");
loadSound("shoppurchase", "/sounds/collectcoin.mp3");
// ... (other sound loading statements)

// Play background music with looping
const gameMusic = play("music", { loop: true });

// Function to create and configure buttons
function addButton(txt, position, func) {
  // Create a button object
  const button = createButton(txt, position);
  
  // Set button hover and click behavior
  button.onHoverUpdate(() => {
    // Scale and cursor change on hover
  });
  button.onHoverEnd(() => {
    // Reset scale on hover end
  });
  button.onClick(func);
  
  return button;
}

// Create Play button
addButton("Play", vec2(width() / 2, height() / 2 - 40), () => { 
  play("menuclick"); // Play a sound
  go("characterSelect"); // Navigate to character select screen
});

// Create How to Play button
addButton("How to Play", vec2(width() / 2, height() / 2 + 60), () => { 
  play("menuclick"); // Play a sound
  go("howToPlay"); // Navigate to how to play screen
});

// Create Credits button
addButton("Credits", vec2(width() / 2, height() / 2 + 160), () => {
  play("menuclick"); // Play a sound
  go("credits"); // Navigate to credits screen
});

// Display the game title
const gameTitle = createGameTitle();
```

## Development

### Outcome

All the game sounds are loaded at the start of the project.

```typescript
loadSound("menuclick", "/sounds/select-sound-121244.mp3");
loadSound("shoppurchase", "/sounds/collectcoin.mp3");
loadSound("startfloor", "/sounds/mixkit-casino-bling.wav");
loadSound("equipweapon", "/sounds/item-equip-6904.mp3");
loadSound("opendoor", "/sounds/open door - Made with Clipchamp.mp3");
loadSound("gamewin", "/sounds/grand-final-orchestral-tutti-9927.mp3");
loadSound("enemyhit","/sounds/hitHurt1.wav");
loadSound("playerdeath", "/sounds/failure notification.wav");
loadSound("playerhurt", "/sounds/hitHurt.wav");
loadSound("bosslevel", "/sounds/boss horn.mp3");
loadSound("shopkeepernote", "/sounds/pickupCoin.wav");
loadSound("recoverhealth", "/sounds/health-pickup-6860.mp3");

loadSound("music", "/sounds/cruising-down-8bit-lane-159615.mp3");
```

The music begins playing when the game starts and will continuously loop. It will play unless paused.

<pre class="language-typescript"><code class="lang-typescript"><strong>const gameMusic = play("music", {loop: true}); 
</strong></code></pre>

The music is paused in the win and lose scenes and unpaused in the main menu scene. Below is an example of the sound which is played when the win scene starts.

```typescript
gameMusic.paused = true;
play("gamewin");
```

I created a new function that adds all the parts of a button and controls what happens on click depending on what is passed into the function. This makes buttons much nicer and makes my code more concise and efficient because the code doesn't have to be duplicated for each button.

<pre class="language-typescript"><code class="lang-typescript"><strong>function addButton(txt, position, func) {
</strong>  // Add a parent background object
  const button = add([
    rect(240, 80, { radius: 8 }),
    pos(position),
    area(),
    scale(1),
    color(242,132,39),
    anchor("center"),
    outline(4),
  ]);

  // Add a child object that displays the text
  button.add([
    text(txt),
    anchor("center"),
    color(0, 0, 0),
  ]);

  // Button is enlarged when hovered over
  button.onHoverUpdate(() => {
    button.scale = vec2(1.2);
    setCursor("pointer");
  });
  
  button.onHoverEnd(() => {
    button.scale = vec2(1);
  });

  // Action runs when the button is clicked
  button.onClick(func);
    
  return button;
};
</code></pre>

Below is an example of how the buttons are made in the main menu.

```typescript
addButton("Play", vec2(width() / 2, height() / 2 - 40), () => { 
        play("menuclick");
        go("characterSelect");
    });
    addButton("How to Play", vec2(width() / 2, height() / 2 + 60), () => { 
        play("menuclick");
        go("howToPlay");
    });
    addButton("Credits", vec2(width() / 2, height() / 2 + 160), () => {
        play("menuclick");
        go("credits");
    });
```

The title for each menu is now a sprite entity rather than a text entity which allows for the exciting font.

```typescript
const gameTitle = add([
        sprite("gametitle"),
        pos(width() / 2, height() / 2 - 290),
        anchor("center"),
        scale(3),
        z(10),
    ]);
```

The player and enemy bullets are both destroyed when they collide with the door.

```typescript
// Destroy bullets when they collide with the door
        onCollide("enemy_bullet", "door", (bullet, door) => {
        destroy(bullet); // Destroy the bullet
    });

    onCollide("player_bullet", "door", (bullet, door) => {
        destroy(bullet); // Destroy the bullet
    });
```

If the enemy is a boss, it will give 5 coins on death, otherwise, it will give one.

{% code title="enemy class.ts" %}
```typescript
destroy() {
        this.isAlive = false;
        if (this.species >= 7) {
            // 5 coins for a boss
            updateCoinCounter();
            updateCoinCounter();
            updateCoinCounter();
            updateCoinCounter();
            updateCoinCounter();
        } else {
            // 1 coin for an ordinary enemy
            updateCoinCounter();
        };
        destroy(this.entity);
    };
```
{% endcode %}

#### New Sprites

<div>

<figure><img src="../.gitbook/assets/cycle18enemy2sprite.png" alt=""><figcaption><p>Enemy 2</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/cycle18shopkeepersprite.png" alt=""><figcaption><p>Shopkeeper</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/cycle18newboss3sprite.png" alt=""><figcaption><p>Final Boss</p></figcaption></figure>

</div>

#### How to Play Graphic

<figure><img src="../.gitbook/assets/cycle18howtoplaygraphic.png" alt=""><figcaption></figcaption></figure>

#### Menu Titles



<figure><img src="../.gitbook/assets/cycle18gametitletext.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/cycle18howtoplaytitletext.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/cycle18creditstitletext.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/cycle18wintitletext.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/cycle18losetitletext.png" alt=""><figcaption></figcaption></figure>

## Challenges

Adding sound to my game was a new experience for me so I had to look quite a bit into the Kaboom documentation but thankfully it is quite similar to adding sprites, which by now I am very familiar with. The only difficult thing with it is making sure the file type is correct otherwise trying to run the game gives lots of errors which I found quite confusing until I realised.

## Testing

### Tests

| Test | Instructions               | What I expect                                                    | What actually happens                               | Pass/Fail |
| ---- | -------------------------- | ---------------------------------------------------------------- | --------------------------------------------------- | --------- |
| 1    | Start game.                | <ul><li>Music plays.</li><li>New buttons look correct.</li></ul> | <ul><li>As expected.</li><li>As expected.</li></ul> | Pass.     |
| 2    | Click on each menu.        | Each button functions correctly.                                 | As expected.                                        | Pass.     |
| 3    | Play through the game.     | Sound effects play for each of the events I added.               | As expected.                                        | Pass.     |
| 4    | Pause and unpause game.    | Music is quieter when paused.                                    | As expected.                                        | Pass.     |
| 5    | Shoot door.                | Bullets are destroyed on collision with door.                    | As expected.                                        | Pass.     |
| 6    | Kill boss.                 | Gives 5 coins.                                                   | As expected.                                        | Pass.     |
| 7    | Go to lose and win scenes. | <ul><li>Music stops.</li><li>Same checks as in test 2.</li></ul> | <ul><li>As expected.</li><li>As expected.</li></ul> | Pass.     |

### Evidence

{% embed url="https://youtu.be/PB5NmFp6gLA" %}
