# URDF models

[Back to MOTKINBENCH](../README.md)

Exported with [onshape-to-robot](https://github.com/rhoban/onshape-to-robot).
Each setup contains a native `config.json`, `robot.urdf`, and STL meshes in `assets/`.

| Setup | Model | Export configuration |
| --- | --- | --- |
| Independent flywheels | [URDF](dual_flywheel/robot.urdf) | [config.json](dual_flywheel/config.json) |
| Five-bar planar robot | [URDF](five_bar/robot.urdf) | [config.json](five_bar/config.json) |

To distribute a model, include `robot.urdf` and its `assets/*.stl` files, keeping
that directory layout. Onshape credentials are only needed to regenerate models.
The URDFs retain the upstream `package://assets/...` mesh paths; configure your
loader's package search path to the setup directory if needed. Generated `.part`
metadata files are ignored by Git and are not needed to load the robots.

## Re-export

Run from the repository root. Set `ONSHAPE_API`, `ONSHAPE_ACCESS_KEY`, and
`ONSHAPE_SECRET_KEY` in your shell or root `.env` file; see [.env.example](../.env.example).

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r urdf/requirements.txt

onshape-to-robot urdf/dual_flywheel
onshape-to-robot urdf/five_bar
```

Both configurations target the live Onshape workspace. Exports write directly
into their setup directory. Assign materials in CAD to obtain mass and inertia;
review any missing-mass warnings from the exporter.

## Preview in PyBullet

With the virtual environment activated, run one viewer at a time:

```bash
python -m pip install pybullet
onshape-to-robot-bullet --fixed --no-self-collisions --z 0.05 urdf/dual_flywheel
onshape-to-robot-bullet --fixed --no-self-collisions --z 0.05 urdf/five_bar
```

All motor and passive elbow joints are continuous, with no angular stops. The
viewer's ±π sliders are only a control range. Effort and velocity use upstream
defaults unless overridden in `config.json`; they are not hardware specifications.

The five-bar URDF is an open tree: command `motor_1` and `motor_2`, leave
`passive_1` and `passive_2` passive, and enforce the closing hinge in your solver.
Match the positions of `closing_tip_1` / `closing_tip_2` and
`closing_tip_1_z` / `closing_tip_2_z`. The standard PyBullet viewer does not add
these constraints. See [upstream loop handling](https://onshape-to-robot.readthedocs.io/en/latest/kinematic_loops.html).
