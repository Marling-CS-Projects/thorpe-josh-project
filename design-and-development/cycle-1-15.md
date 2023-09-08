# 2.2.14 Cycle 14 - Boss Fights

## Design

### Objectives

In this cycle, I will. My objectives in this cycle are:

* [ ] Add spikes to boss levels
* [x] Rebalance the guns and enemies
* [ ] Add boss enemies as new enemy types to the enemy class
* [ ] Bosses are bigger and stronger than normal enemies
* [ ] Boss 2 shoots in bursts
* [ ] Boss 3 shoots like a shotgun
* [ ] Bosses can spawn more enemies around them
*

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
// Some code
```

explain some more

```typescript
// Some more code
```

### Challenges

Initially, I planned to implement bosses using a separate boss class with the special boss behaviour which I could then call when a boss level started.

```typescript
// Some code
```

However, I kept getting strange behaviour and errors despite most of the code being duplicated from the Enemy class. This confused me and I realised that it would be much simpler to use the Enemy class for bosses and add a few special functions which only the boss enemies call. Below is a video of my first attempt at bosses.



## Testing

### Tests

| Test | Instructions   | What I expect      | What actually happens | Pass/Fail |
| ---- | -------------- | ------------------ | --------------------- | --------- |
| 1    | Run code.      | Thing happens.     | As expected.          | Pass.     |
| 2    | Press buttons. | Something happens. | As expected.          | Pass.     |

Comment on any failed tests and how you plan to resolve them

### Evidence

Put YouTube video testing link here
