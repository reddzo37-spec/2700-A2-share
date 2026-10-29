# Mech2700 A2: Orion Wall Heat Transfer

Finite difference of transient 2D heat conduction through the Orion back shell wall, with radiative heating, convective cooling
and periodic boundary conditions.

Author :   [Zoe Reddicliffe (49613753), Sophie Vandemeer (49217393)]

Requirements:
- Python 3.9 or later
- NumPy
- Matplotlib

Install with: `pip install numpy matplotlib`

| File | Purpose |
|---|---|
| `A2_Task_4.py` | Solver module (Task 4): constants, boundary heating, `step()` and `run_simulation()`. Not run directly for results. |
| `A2_Task_5.py` | Task 5: baseline contour plots and grid-refinement study. |
| `A2_Task_6.py` | Task 6: rotation-rate study, critical rotation periods, comfort-limit checks. |

All three files must be in the same folder. The task scripts import the solver with `import A2_Task_4`, the solver file name must match
that import.

## How to run

Open a terminal in the folder containing the files, then run:

```
python orion_wall.py     # Task 4: quick demonstration on the coarse grid
python task5.py          # Task 5
python task6.py          # Task 6
```

### Task 4 : `A2_Task_4.py`

Running this file performs a single coarse-grid simulation (nx=17, ny=159, rotation period 1000 s, t_max = 7200 s) and prints the grid spacings, time step, number of steps, snapshot times and final maximum inner-wall temperature.

The main functions are:

- `step(T, t, dt, dx, dy, y, omega)`: which advances the temperature array by one explicit time step ( interior update, then x-boundaries, then periodic y-boundaries)
- `run_simulation(nx, ny, omega, t_max, t_plot, safety=0.9)`: goes from t=0 to t_max. The time step is at most safety times the stability limit and is adjusted so that t_max is an integer number of steps. It returns a dictionary of the stored snapshots (every `t_plot` seconds to within one time step) and of the maximum and minimum inner-wall temperature, as well as the maximum domain temperature at every step.

### Task 5: `A2_Task_5.py`

This runs the basline case (rotation period 1000s) on three grids with spacingd reduced by factors of 1, 2 and 4 (nx = 12, 33, 65 and ny = 159, 317, 633)

Outputs:
-`task5_contours.png`: temperature contours at t=0,1442, 2882, 4321, 5761 and 7200 s (coarse grid).
-`task5_Twmax_coarse.png`: maximum inner-wall temperature against time (coarse grid).
-`task5_grid_comparision.png`: maximum inner wall temperature histories for all three grids.
- A table printed to the terminal: grid sizes, spacings, time step, number of steps, run time, peak T_w,max and T_w,max at t_max.

### Task 6: `A2_Task_6.py`

Uses the grid set by `NX, NY` near the top of the file (grid selected in Task 5) and runs four cases: no rotation, the baseline (1000 s period), the maximum allowable rate (120 s period) and the recommended period set by `RECOMMENDED_PERIOD`.

Settings you can edit at the top of the file:
- `NX,NY` : grid size
- `RECOMMENDED_PERIOD`: recommended rotation period in seconds.

Outputs:
- `task6_Twmax_rotation.png`: maximum inner-wall temperature histories for all rotation rates, with the 308 K limit.
-`task6_Twmin_rotation.png`: minimum inner-wall temperature histories, with the 283 K limit.
-`task6_period_scan.png`: peak and lowest inner-wall temperature against rotation period.
- Terminal output: peak t_wmax, the time it first exceeds 308 K, the lowest inner-wall temperature and the highest temperature anywhere in the    wall for each case, plus the critical rotation periods (found by bisection) for the 308 K and 283 K limits.

### Run times
Run times depend on the machine, all tasks should run in a minuite or less.

### Notes
- Array convention: `T[i, j]`, with i indexing x (wall thickness) and j indexing y (around the circumference). Units are SI throughout.
- The time step is set from the von Neumann stability limit `dt_max = 1/ (2 * alpha * (1/dx**2 + 1/dy**2))` with a safety factor of 0.9.
- Periodic boundaries copy the updated interior rows from the opposite side.
- The nonlinear radiation term is treated explicity (evaluated at the old time level)