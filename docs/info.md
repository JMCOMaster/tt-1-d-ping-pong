<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

## How it works

This design is a fundamental 1D Pong game built entirely with digital logic gates and flip-flops. The inputs consist of three main buttons (Player 1, Player 2, and Start), and the output is displayed across a row of 8 LEDs. When a game begins, a single LED lights up to represent the ball. Players must press their respective buttons at the exact right moment to bounce the ball back and avoid losing.

The design relies on several distinct functional blocks working together:

### Bidirectional Shift Register (Ball Movement Logic)
The core of the game is a bidirectional shift register built using **D-type Flip-Flops** and **2-to-1 Multiplexers**. A single active bit (the ball) shifts sequentially across the Flip-Flop outputs. By toggling the multiplexers' select line, the system dictates whether the Flip-Flops listen to their left or right neighbor, effectively changing the ball's shifting direction. 

### Anti-Bounce & Edge Detection
To prevent erratic behavior, an anti-bounce circuit is implemented using two Flip-Flops and logic gates. This acts as an edge detector, ensuring that even if the 'Start' button is held down, only a single, clean pulse is generated. This prevents the system from spawning multiple balls simultaneously.

### Game State Interlock (LED Monitoring)
An expansive **OR-gate tree** continuously monitors the state of all 8 LEDs. This acts as a hardware interlock: it guarantees that the 'Start' button is completely disabled as long as there is an active ball already in play on the LED row.

### Clock Domain Partitioning
To make the system highly responsive while keeping the game playable, the architecture is divided into two clock domains: a **32 Hz Master Clock** and a **2 Hz Game Clock**. 
* **User Experience (32 Hz):** The master frequency drives the anti-bounce logic and button synchronization. This high polling rate ensures that the hardware catches fast button presses instantly, meaning players don't have to hold down the buttons to register a hit.
* **Game Speed (2 Hz):** The LED shift logic operates on the slower 2 Hz clock, moving the ball at a speed that human reflexes can actually track and react to.
* **Clock Divider Implementation:** To derive the 2 Hz signal from the 32 Hz master clock, four D Flip-Flops are cascaded in a toggle configuration. Each Flip-Flop divides the incoming frequency by half, successfully achieving the target frequency ($32\text{ Hz} / 2^4 = 2\text{ Hz}$).

*(Note: Future versions could implement dynamic frequency scaling to gradually increase the game clock speed, making the gameplay more competitive).*

### Direction Control (1-Bit FSM)
This block handles the bounce logic based on player reaction. First, it verifies that the ball is positioned at the exact edge of the player's side when their button is pressed. These impact signals are fed into a synchronous Latch (a **1-Bit Finite State Machine** built around a D Flip-Flop). Based on the players' inputs and its current internal state, the FSM updates the ball's direction and securely memorizes it until the next valid hit is registered.

*(Note: There's a Reset Button but it doesn't work i didn't implement the logic to it).*

## How to test

Hit the start button and avoid to get the light out of the leds with the Player 1&2 Buttons, better to use with 2 players.

## External hardware

-3 Mechanical Buttons

-8 LED's
