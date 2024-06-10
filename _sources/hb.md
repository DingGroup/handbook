---
title: Hb
marimo-version: 0.6.14
---

# OpenMM

```{code} python
import openmm as mm
import openmm.app as mm_app
import openmm.unit as unit
import numpy as np
import matplotlib.pyplot as plt
```

Create a system and add two particles to it

```{.python.marimo}
system = mm.System()

system.addParticle(1.0 * unit.amu)
system.addParticle(12.0 * unit.amu)
```

Add a force

```{.python.marimo}
force = mm.HarmonicBondForce()
force.addBond(
    0, 1, 1.0 * unit.angstrom, 10 * unit.kilojoule_per_mole / unit.angstrom**2
)
system.addForce(force)
```

```{.python.marimo}
integrator = mm.LangevinMiddleIntegrator(300 * unit.kelvin, 1.0 / unit.picosecond, 0.002 * unit.picosecond)

platform = mm.Platform.getPlatformByName('Reference')
context = mm.Context(system, integrator, platform)

positions = np.array([[0.0, 0.0, 0.0], [1.0, 0.0, 0.0]], np.float32) * unit.angstrom
context.setPositions(positions)
```

```{.python.marimo}
xyz = []
us = []
for i in range(3000):
    integrator.step(1000)
    state = context.getState(getEnergy=True, getPositions=True)
    xyz.append(state.getPositions(asNumpy=True).value_in_unit(unit.angstrom))
    u = state.getPotentialEnergy().value_in_unit(unit.kilojoule_per_mole)
    us.append(u)
    if (i + 1) % 100 == 0:
        print(i, f'{u:.3f} kJ/mol')

xyz = np.array(xyz)
```

```{.python.marimo}
d = np.linalg.norm(xyz[:, 1, :] - xyz[:, 0, :], axis=1)

plt.figure()
plt.clf()
plt.hist(d, bins=20, density=True)
plt.xlabel('Distance (angstrom)')
plt.ylabel('Probability Density')
plt.tight_layout()
plt.savefig('hist.png')
plt.gca()
```

```{.python.marimo}
plt.figure()
plt.clf()
plt.plot(d[::10], linewidth=1)
plt.xlabel('Time (ps)')
plt.ylabel('Distance (angstrom)')
plt.tight_layout()
plt.savefig('d.png')
plt.gca()
```

```{.python.marimo}

```

```{.python.marimo}
import marimo as mo
```