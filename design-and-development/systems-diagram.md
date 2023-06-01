# 2.1 Design Frame

## Systems Diagram

![](https://github.com/Marling-School/alevel-project-template/blob/docs/.gitbook/assets/System%20Diagram.jpg)

This diagram shows the different parts of the game that I will focus on creating. I have split each section into smaller sub-sections. Throughout the development stage, I will pick one or two of these sections to focus on at a time to gradually build up and piece together the game. I have broken the project down this way as it roughly corresponds to the success criteria.

## Usability Features

Usability is an important aspect of my game as I want it to be accessible to as many people as possible. There are 5 key points of usability to create the best user experience that I will be focusing on when developing my project. These are:

### Effective

It's important that users are able to complete my game with completeness and accuracy. To do this, I will ensure it is easy for players to understand how to win by introducing the objective at the start of the game. Additionally, I will make the path through each floor clear so that there is no confusion over where the players need to go.

#### Aims

* Create a clear goal to reach to determine the end of a level
* Create a clear goal for any multiplayer modes

### Efficiency

Efficiency refers to the speed and accuracy with which a user can complete the goal. To maximise efficiency, I will create a menu system which is easy to navigate through in order to find what you are looking for. Also, I will keep informational dialogue concise to avoid unnecessary complexity.

The controls too must also be efficient in that they are intuative to use and allow the user to carry out multiple actions.

#### Aims

* Create a menu system that is quick and easy to navigate through
* Create a controls system that isn't too complicated but allows the player to do multiple actions

### Engaging

It is really important that my game is fun and engaging to play. To do this, I will create 3 floors (levels) and include multiple different weapons to keep players engaged and allow them to have fun while playing the game. Additionally, randomised chest and shop items and procedurally generated floors will keep the game engaging and exciting as no two runs are the same. Also, using a pixel art style may help draw in more people as pixel art games have become widely popular in recent years. [(Rocket Brush Studio, 2021)](../analysis/reference-list.md#2.1-design-frame)

#### Aims

* Create a series of levels to work through
* Create a multiplayer mode to play
* Incorporate a style of game art the suits the game

### Error Tolerant

My game should have as few errors as possible and if an error does occur, it should be corrected by my program. To do this, I will write my code so that my game will function in as many different game scenarios as possible to ensure that it will not crash when someone is playing it.

#### Aims

* The game doesn't crash
* The game does not contain any bugs that damage the user experience

### Easy To Learn

My game should be easy to play and not be overly complicated. To do this, I will create simple controls for the game as outlined [here](../analysis/1.4a-features-of-the-proposed-solution.md#controls). I will make sure that no more controls are added than are needed in order to keep them as simple as possible for the players.

#### Aims

* Create a list of controls for the game
* Create an in-level guide that helps players learn how to play the game

## Pseudocode for the Game

### Pseudocode for game

This is the basic layout of the object to store the details of the game. This will be what is rendered as it will inherit all important code for the scenes.

```
object Game
    type: Phaser
    parent: id of HTML element
    width: width
    height: height
    physics: set up for physics
    scenes: add all menus, levels and other scenes
end object

render Game to HTML web page
```

### Pseudocode for a level

This shows the basic layout of code for a Phaser scene. It shows where each task will be executed.

```
class Level extends Phaser Scene

    procedure preload
        load all sprites and music
    end procedure
    
    procedure create
        start music
        draw background
        create players
        create platforms
        create puzzle elements
        create enemies
        create obstacles
        create finishing position
        create key bindings
    end procedure
    
    procedure update
        handle key presses
        move player
        move interactable objects
        update animations
        check if player at exit
    end procedure
    
end class
```
