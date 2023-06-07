# nandgame_solutions
solutions for nandgame.com (Hardware as pictures in folder Hardware)

## Software

### 1.4 Escape Labyrinth
The computer is stuck in a labyrinth on Mars. Write a program that will make it escape the labyrinth.

The computer has connected wheels and a forward obstacle detector. Input/output to wheels and detector is memory-mapped on address 7FFF:

Output signals to peripherals:

Bit	Set to 1 to:
2	Move forward (1 step)
3	Turn left (90 degrees)
4	Turn right (90 degrees)
The movement/turn is started when a bit is changing from 0 to 1, but will take a moment to complete.

Input from peripherals:

Bit	When 1
8	Obstacle detected in front
9	Device is turning
10	Device is moving forward

```
# Assembler code 

# move forward    0b100
# turn  left     0b1000
# turn right    0b10000

# device is blocked       0b1_0000_0000
# device is turning      0b10_0000_0000
# device moving forward 0b100_0000_0000
# is moving/turning     0b110_0000_0000

# memory adress of sensor 0x7FFF

LABEL isInMotion
# define bits to check
A = 0b110_0000_0000
D = A
# get current status
A = 0x7FFF
A = *A
# are we moving?
D = D & A
# if D false we can move 
#   (not moving/turning)
A = tryMoveForward
D ; JEQ
# else we loop this block
A = isInMotion
JMP

LABEL tryMoveForward
# check blocked -> turn right
A = 0b1_0000_0000 
D = A
A = 0x7FFF
A = *A
D = D & A
A = turnRight
D ; JNE
# else we move forward
A = 0b100
D = A
A = 0x7FFF
*A = D
A = isInMotion
JMP

LABEL turnRight
# nothing else we can do but turn
A = 0b10000
D = A
A = 0x7FFF
*A = D
A = isInMotion
JMP
```
