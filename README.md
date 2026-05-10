An (WIP) implementation of Pong using discrete logic. Digital (hneeman) is being used for the simulations, and the 74LSxx is the primary family of chips being used for the logic.

After the simulation is completed, I'll build it out on a breadboard before moving to a PCB if time permits!

## Subsystems:

| System                        | Status      | Notes                                                                       |
| ----------------------------- | ----------- | --------------------------------------------------------------------------- |
| VGA Driver                    | Complete    | Implemented with discrete ICs. Generic NOT gates left in for wiring sanity. Maybe switch out ANDs in CLR logic to NANDs. |
| Paddle Rendering              | Complete    | Complete and optimised. Uses XNORs for comparison (equality) a counter for the Y instead of an adder and a latch.|
| Paddle Movement               | Complete    | Novel clock pass through to contol direction of counters individually. |
| Ball Rendering                | WIP           | -                                                                           |
| Ball Movement                 | WIP           | -                                                                           |
| Collision Detection + Rebound | -           | -                                                                           |
| Score Keeping                 | -           | -                                                                           |
| Master Reset                  | Complete    | Simple latch. Has a START and RESET button. Enables global rendering pipeline. |








