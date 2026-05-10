## VGA Driver

Output signal is using VGA. I tried to stay close to spec, but the clock speed of 25MHz for a breadboard implementation is pretty ambitious, so it has been stepped down to 10MHz. That is the Master CLK. The resolution we're running at is 640x480. At 25Mhz, we could expect 60Hz. 

To track the scanline progression, we have 2x12bit cascading counters:

<img width="405" height="730" alt="image" src="https://github.com/user-attachments/assets/990ac176-f70e-4174-a911-db0b1dc0a5ac" />

10 bit equality comparison is done with a constant using 2NANDs (8 bits) and 4 ANDs (remaining 2 bits and tying the two signals), thus producing the CLR signal to keep the scanline in bounds.

<img width="687" height="291" alt="image" src="https://github.com/user-attachments/assets/8aa61a26-0b77-443d-aaff-a33ffcea848c" />

Then we need to produce the SYNC signal. In essence, we need to pull the signal low within a certain threshold. Each end of the equality that we need to pull the signal low in done with one 8 input NAND and half a quad AND IC. 

<img width="729" height="594" alt="image" src="https://github.com/user-attachments/assets/d2579da0-377b-427e-ba00-35591a3ad1af" />


## Middle Court Net

Renders a dashed line halfway through the screen in an 8 on 8 off pattern. 

<img width="812" height="586" alt="image" src="https://github.com/user-attachments/assets/3de7f8ea-c9bc-4e7a-a6af-23d32b064b14" />

## Paddle Control

We use another clock, the Physics CLK for the paddle movement. When either the up or the down button is pressed, the clock signal is passes through by using an AND (when button goes HIGH, the output relies only on the CLK, essentially acting as a gate, letting the signal through).

## Paddle Y Counters

Uses 3x4bit = 12bit cascading U/D counters to track the Y position. Direction is determined by the previous stage. CLK is the passthrough output of the previous stage too. Currently no logic to prevent the paddle from going of screen. It does return (eventually) in such a case. The 7408 in the middle produces the clock signal to move it up or down depending on inputs.

<img width="830" height="700" alt="image" src="https://github.com/user-attachments/assets/c8c7ee1a-d8ea-45a6-b20a-8a5395366bd8" />


## Paddle Rendering

X rendering is trivial. Using a bitmask, and a few XNORs we pull the signal low for 16 counts in the specified range. Since its a clean power of 2, we just look at the necessary bits (H3-H9) and compare them to 0 or 1 as per the mask. The outputs of the NANDs are ANDed and we have the X signal. The Y signal is a bit more involved. It uses XNOR comparison again, but this time for equality using another variable instead of a constant. We turn the signal high when its above the Y coordinate from our paddle counters. This then will SET a latch HIGH. The output of the latch is tied to the ENABLE of a 6bit counter (2x4bit cascaded). When the 7th bit tunrs to 1 (i.e our idealised 6 bit counter overflows), we have a CLR signal that is used to reset the counter and RESET the latch. In effect, this pulls the paddle's Y signal low for 64 counts after the scanline passes the top Y coordinate. This gives the paddle dimensions of 64x16 bits. I would have preffered 60x10 which is what my original logic had, but optimising that for both propagation delay and IC count isn't very fun, so I settled for the easier to work with resolution I have now. You could theoretically implement any arbitrary dimension you like by doing hte maths and calculating the actual time you want to pull the signal low, instead of using my coordinate system approach, similar to the original implementation. Either way, I then just use an AND on the X and Y signals, as as a result, we have a paddle rendered when both our signals are HIGH.

<img width="940" height="450" alt="image" src="https://github.com/user-attachments/assets/1d44b9e0-a6b8-4497-a027-3ce182ee097c" />
