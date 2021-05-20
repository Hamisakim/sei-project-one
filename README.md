# Hungry Shark ReadMe
Hungry Shark - A Pac-Man spinoff (unofficial)
The first solo project during my study at General Assembly's Software Engineering Immersive. 
# Deployment
The game is deployed and playable [here](https://hamisakim.github.io/sei-project-one/) 

# Concept 
This game is based on the hugely popular 80's arcade game Pac-Man. 
The aim of the game is for the player to eat all the food in an arena while trying to avoid being eaten by the four ghosts that are hunting them.
If the player eats special items then they can eat the ghosts for a limited time and send them back to their start location, and gain extra points in the process. 

The game ends when there is no more food or the player has run out of lives. 

# Project brief
* One week to plan and create a game in vanilla JavaScript, HTML, and CSS 
* The player should be able to clear at least one board.
* The player's score should be displayed at the end of the game
Technologies:
* JavaScript (ES6)
* HTML 5
* HTML audio
* CSS
* Google Fonts
* GitHub


# Game instructions
Use the WASD or arrow keys to move Bruce the shark and eat all the fish while avoiding the enemies. 
Eating a turtle will provide invincibility. 

# Approach 
I made the grid via a function where the cell count is defined and then used in a for loop. Initially this was a simple 10x10 grid to get the basic movement for Bruce. 
<img width="1143" alt="Screenshot 2021-02-19 at 12 36 06" src="https://user-images.githubusercontent.com/76621344/118801358-d26f9580-b898-11eb-8b90-24fcf4303c21.png">

Later this was scaled to be much bigger, closer to the original Pac-man map.


```javascript
    const gameGrid = document.querySelector('.game-grid')
    const width = 20
    const height = 25
    const cellCount = width * height

function createGrid() {
      for (let i = 0; i < cellCount; i++) {
        const cell = document.createElement('div')
        //cell.textContent = i //!number on
        gameGrid.appendChild(cell)
        positionArray.push(cell)
        if ((i % 20 === 0) || (i > 480) || (i < 20) || ((i + 1) % 20 === 0)) {
          cell.classList.add('wall')
        } else if (walls.includes(i) === true) {
          cell.classList.add('wall')
        } else if (cage.includes(i) === true) {
          cell.classList.add('cage')
        } else if (food.includes(i) === true) {
          cell.classList.add('food')
        }
        if (i === 260 || i === 279) {
          cell.classList.remove('wall')
        } if (powerUps.includes(i) === true) {
          cell.classList.add('power-up')
        } if (i === 229 || i === 230) {
          cell.classList.add('cage-wall')
        } if (intersection.includes(i) === true) {
          cell.classList.add('intersection')
        }
      }

```
 The code here allows the user to move through the tunnels on the edge of the map
 ```
 if (i === 260 || i === 279) {
          cell.classList.remove('wall')
        } 
```




For the map design, I used a spreadsheet to visualise the grid easier. 
Here we can see how the green spaces are useable squares for the characters. The yellow is the pen for the enemy characters, purple for intersections.

![Screenshot 2021-05-10 at 12 20 28](https://user-images.githubusercontent.com/76621344/117825121-f3b3fe80-b266-11eb-8945-4fbca46dee97.png)
![Screenshot 2021-05-10 at 12 25 15](https://user-images.githubusercontent.com/76621344/117825128-f44c9500-b266-11eb-852b-d3922fe6b771.png)



# Movement 
To streamline the app I made four functions for each direction.  This will also update the character to face the correct direction! 
``` javascript
  function moveRight(character) {
      const wallToRight = positionArray[character.currentPosition + 1].classList.contains('wall')
      if (wallToRight === false) {
        positionArray[character.currentPosition + 1].classList.add('swim-right')
        positionArray[character.currentPosition].classList.remove('swim-left', 'swim-up', 'swim-down', 'swim-right', 'enemyOnSquare')
        character.currentPosition++
      }
    }
```
# Character AI
To make the enemies move randomly was easy, but I definitely underestimated the complexity of each character’s unique movement based on their target tile. For more details on how this works in the real Pac-man refer [here](https://www.gamasutra.com/view/feature/132330/the_pacman_dossier.php?page=6). 

The enemies move on an interval timer, in this timer we have the smartMoveEnemy function.  This function will check the surrounding enemy tiles and prevent the enemy from trying to move through a wall, and if the enemy is at an intersection. 

When the character arrives at an intersection it must choose which direction to go to get closer to its target tile. This is done via the atIntersection function.
In order to find the shortest distance as a straight line we have to use some Pythagorean  maths. The findC function handles this for us and returns the straight line distance!
``` javascript
    function atIntersection(enemy) {
      const distanceSquareAbove = findC(enemy.currentPosition - width, enemy.targetCell)
      const distanceSquareBelow = findC(enemy.currentPosition + width, enemy.targetCell)
      const distanceSquareRight = findC(enemy.currentPosition + 1, enemy.targetCell)
      const distanceSquareLeft = findC(enemy.currentPosition - width, enemy.targetCell)

      const wallToRight = positionArray[enemy.currentPosition + 1].classList.contains('wall')
      const wallToLeft = positionArray[enemy.currentPosition - 1].classList.contains('wall')
      const wallBelow = positionArray[enemy.currentPosition + width].classList.contains('wall')
      const wallAbove = positionArray[enemy.currentPosition - width].classList.contains('wall')

      if (distanceSquareAbove < distanceSquareBelow) {
        if (!wallAbove) {
          moveUp(enemy)
        } else if (!wallToLeft) {
          moveLeft(enemy)
        } else if (!wallBelow) {
          moveDown(enemy)
        }
      } else if (distanceSquareBelow < distanceSquareAbove) {
        if (!wallBelow) {
          moveDown(enemy)
        } else if (!wallToLeft) {
          moveLeft(enemy)
        } else if (!wallToRight)
          moveRight(enemy)
      } else if (distanceSquareAbove === distanceSquareBelow) {
        if (distanceSquareRight < distanceSquareLeft && !wallToRight) {
          moveRight(enemy)
        } else if (distanceSquareLeft < distanceSquareRight && !wallToLeft) {
          moveLeft(enemy)
        }
      }
    }

    function findC(enemyPosition, targetCell) {
      const positionDiff = (enemyPosition - targetCell)
      const yDistance = Math.round(positionDiff / 20)
      const xDistance = -1 * (positionDiff - yDistance * 20)
      const c2 = Math.pow(xDistance, 2) + Math.pow(yDistance, 2)
      const c = Math.sqrt(c2)
      return c
    }
```

# Wins and Challenges
## Challenges 😧
* While it was straightforward to make enemies move randomly giving them AI was much harder than expected! 
* A key difficulty was using the grid. It not being an x,y grid made things a little harder with the maths. 
## Wins 🏆
* I am so proud of this game! I’m very happy with the entry screen especially.
* After only a month of coding, this is what I was able to produce!  This was great for my self-confidence. 


# Key Learnings and Takeaways 
This project allowed me to cement my knowledge of the DOM and JavaScript fundamentals. As well as a tiny introduction into AI. 
The importance of pseudocode and planning before coding is something I took away from this and something I constantly try to implement now.

# Bugs to address 🕷
* The mute button doesn't unmute. It currently just removes the audio.
* the play again button will reload the whole site. I need to make this just reload the game 
* The movement isn't the cleanest so i'd like to add smoother movements.



