# Vibrational energy transport in 2D kink chains

MATLAB and Python codes for computing phonon scattering (transmission and reflection) and thermal conductivity in 2D atomic chains containing geometric kink defects.

---

## Physical background

The model is a 2D chain of atoms connected by nearest-neighbour (spring constant `A`) and next-nearest-neighbour (spring constant `B`) springs. The equilibrium chain is a zigzag with inter-bond angle `ph`. A **kink** is a point where the chain axis rotates by angle `ph1`. Phonons incident on one or more kinks are partially transmitted and partially reflected, and can scatter between the two polarisations (longitudinal LA ↔ transverse TA).

Two types of calculation are provided:

- **Phonon scattering** (Codes2): transmission and reflection coefficients T, R at a single frequency for a single kink.
- **Thermal conductivity** (Codes1): thermal conductivity κ obtained by multiplying the frequency-integrated transmission by the total chain length k·l, then averaging over many random kink configurations. The factor k·l converts thermal conductance (which decays as 1/L) into the intensive thermal conductivity (independent of system size in a bulk material).

---

## Files

### Codes2 — phonon scattering at a single kink

| MATLAB | Python module | Purpose |
|---|---|---|
| `BlocksCoordGenRot.m` | `transport.blocks_coord_gen_rot` | Build Hessian and extract coupling blocks for a single rotated kink |
| `BlocksCoordGenRotDef.m` | `transport.blocks_coord_gen_rot_def` | Same with a displaced junction atom |
| `Transmission.m` | `transport.transmission` | Total T at given ω |
| `GetTrsl.m` | `transport.get_trsl` | T, R for longitudinal incidence |
| `GetTrst.m` | `transport.get_trst` | T, R for transverse incidence |
| `SelfEnerg.m` | `transport.self_energ` | Lead self-energy matrices |
| `TRGF.m` | `transport.trgf` | Green-function transmission (alternative method) |
| `HessianRotKink.m` | `geometry.hessian_rot_kink` | Hessian in rotated frame |
| `KinksCoord.m` | `geometry.kinks_coord` | Single kink coordinates |
| `SpectrumOm.m` | `core.spectrum_om` | Dispersion relation / wavevectors |
| `RegSpectrq.m` | `core.reg_spectr_q` | Eigenvector and group velocity at q |

### Codes1 — thermal conductivity of multi-kink chains

| MATLAB | Python module | Purpose |
|---|---|---|
| `ThermCondKinksRealDefRestr.m` | `thermal.therm_cond_restr` | κ (thermal conductivity) for strongly-aligned chains (bounded vertical displacement) |
| `ThermCondKinksRealRWDef.m` | `thermal.therm_cond_rw` | κ (thermal conductivity) for random-walk chains |
| `BlocksCoordDef.m` | `thermal.blocks_coord_def` | Hessian and coupling blocks for arbitrary coordinates |
| `Hessian2DOpDef.m` | `thermal.hessian_2d_op_def` | Hessian with modified NNN spring at kinks |
| `TransmVect.m` | `thermal.transm_vect` | T(ω) evaluated at an array of frequencies |
| `OmMax.m` | `thermal.om_max` | Maximum phonon frequency (upper integration limit) |
| `GenerateRestrKinksCoord.m` | `generate.generate_restr_kinks_coord` | Random restricted-geometry chain coordinates |
| `GenerateRealKinksRWCoord.m` | `generate.generate_real_kinks_rw_coord` | Random walk chain coordinates |

---

## Parameters

| Symbol | Description | Typical value |
|---|---|---|
| `A` | NN spring constant | 4.8 |
| `B` | NNN spring constant | 1.0 |
| `ph` | Inter-bond (tilt) angle (radians) | π/4 ≈ 0.7854 |
| `ph1` | Kink rotation angle (radians) | π/4 |
| `n` | Number of lead sites included on each side | 6 (must be even, > 4) |
| `l` | Mean spacing between kinks | 2 or 4 |
| `k` | Number of kinks (must be divisible by 4) | 16–20 |
| `xi` | Ratio of NNN spring at kink to bulk value | 1.0 |
| `Ym` | Max vertical displacement for restricted chains | 2 or 4 |
| `n1`, `n2` | First and last random realisation index | e.g. 1, 50 |

---

## Phonon scattering — quick start

### MATLAB

```matlab
A = 4.8; B = 1.0; ph = pi/4; ph1 = pi/4;
om = 0.8;    % frequency inside the propagating band

% Build Hessian and extract coupling blocks
yz = BlocksCoordGenRot(A, B, ph, ph1, 6);
H  = yz.Hin;
Vl = yz.Vl;
Vr = yz.Vr;

% Compute transmission and reflection coefficients
[T_total, Tst, Trl, Trt, z] = Transmission(om, Vl, Vr, H, A, B, ph);

disp(['Total T = ', num2str(T_total)]);
disp('z.TRL = [T_ll, T_tl, R_ll, R_tl] for longitudinal incidence:');
disp(z.TRL);
disp('z.TRT = [T_lt, T_tt, R_lt, R_tt] for transverse incidence:');
disp(z.TRT);
```

### Python

```python
import numpy as np
from vib_transport import blocks_coord_gen_rot, self_energ, transmission

A, B, ph, ph1 = 4.8, 1.0, np.pi/4, np.pi/4
om = 0.8    # frequency inside the propagating band

# Build Hessian and extract coupling blocks
blk = blocks_coord_gen_rot(A, B, ph, ph1, 6)
H   = blk["Hin"]
Vl  = blk["Vl"]
Vr  = blk["Vr"]

# Compute transmission and reflection coefficients
T_total, Tst, Trl, Trt, z = transmission(om, Vl, Vr, H, A, B, ph)

print(f"Total T = {T_total:.4f}")
print("z['TRL'] = [T_ll, T_tl, R_ll, R_tl] for longitudinal incidence:")
print(z["TRL"])
print("z['TRT'] = [T_lt, T_tt, R_lt, R_tt] for transverse incidence:")
print(z["TRT"])
```

**Output meaning:**

- `TRL[0]` = T_ll: longitudinal → longitudinal transmission
- `TRL[1]` = T_tl: longitudinal → transverse transmission (mode conversion)
- `TRL[2]` = R_ll: longitudinal → longitudinal reflection
- `TRL[3]` = R_tl: longitudinal → transverse reflection
- `TRT` gives the same four quantities for transverse incidence
- `TRL.sum()` and `TRT.sum()` should each equal 1 (energy conservation check)

---

## Transmission spectrum

### MATLAB

```matlab
A = 4.8; B = 1.0; ph = pi/4; ph1 = pi/4;
yz = BlocksCoordGenRot(A, B, ph, ph1, 6);
H = yz.Hin; Vl = yz.Vl; Vr = yz.Vr;

Om_vec = linspace(0.05, 1.0, 200);
T_ll = zeros(size(Om_vec));
T_tl = zeros(size(Om_vec));

for ii = 1:length(Om_vec)
    om = Om_vec(ii);
    [~, ~, ~, ~, z] = Transmission(om, Vl, Vr, H, A, B, ph);
    T_ll(ii) = z.TRL(1);
    T_tl(ii) = z.TRL(2);
end

plot(Om_vec, T_ll, Om_vec, T_tl);
legend('T_{ll}', 'T_{tl}'); xlabel('\omega'); ylabel('Transmission');
```

### Python

```python
import numpy as np
import matplotlib.pyplot as plt
from vib_transport import blocks_coord_gen_rot, transmission, spectrum_om

A, B, ph, ph1 = 4.8, 1.0, np.pi/4, np.pi/4

blk = blocks_coord_gen_rot(A, B, ph, ph1, 6)
H, Vl, Vr = blk["Hin"], blk["Vl"], blk["Vr"]

om_values = np.linspace(0.05, 1.0, 200)
T_ll, T_tl, R_ll = [], [], []
om_good = []

for om in om_values:
    # skip frequencies outside the propagating band
    if abs(spectrum_om(A, B, ph, om).Wv[0].imag) > 1e-4:
        continue
    try:
        T_total, Tst, Trl, Trt, z = transmission(om, Vl, Vr, H, A, B, ph)
        if np.any(np.abs(Tst) > 0.1):
            continue   # normalisation failed — skip
        om_good.append(om)
        T_ll.append(z["TRL"][0])
        T_tl.append(z["TRL"][1])
        R_ll.append(z["TRL"][2])
    except np.linalg.LinAlgError:
        continue

plt.plot(om_good, T_ll, label="T_ll")
plt.plot(om_good, T_tl, label="T_tl (mode conversion)", linestyle="--")
plt.plot(om_good, R_ll, label="R_ll", linestyle=":")
plt.xlabel("ω"); plt.ylabel("Coefficient")
plt.legend(); plt.tight_layout(); plt.show()
```

---

## Thermal conductivity — quick start

### MATLAB

**Strongly-aligned (restricted) chains:**

```matlab
l=2; k=16; A=4.8; B=1.0; ph=pi/4; ph1=pi/4; n1=1; n2=50; xi=1.0; Ym=2;
y = ThermCondKinksRealDefRestr(l, k, A, B, ph, ph1, n1, n2, xi, Ym);
disp(['Mean kappa = ', num2str(y.k)]);
disp(['Relative error = ', num2str(y.Dk)]);
```

**Random-walk chains:**

```matlab
l=2; k=16; A=4.8; B=1.0; ph=pi/4; ph1=pi/4; n1=1; n2=50; xi=1.0;
y = ThermCondKinksRealRWDef(l, k, A, B, ph, ph1, n1, n2, xi);
disp(['Mean kappa = ', num2str(y.k)]);
disp(['Relative error = ', num2str(y.Dk)]);
```

Each realisation result is appended to a text file whose name encodes all parameters. At the end, `y.k` is the mean conductance and `y.Dk` is the relative standard error.

### Python

**Strongly-aligned (restricted) chains:**

```python
from vib_transport import therm_cond_restr

result = therm_cond_restr(
    l=2, k=16,
    A=4.8, B=1.0, ph=0.7854, ph1=0.7854,
    n1=1, n2=50,
    xi=1.0, Ym=2.0
)
print(f"Mean kappa = {result['kappa']:.6f}")
print(f"Relative error = {result['Dk']:.4f}")
print(f"Per-realisation values: {result['values']}")
```

**Random-walk chains:**

```python
from vib_transport import therm_cond_rw

result = therm_cond_rw(
    l=2, k=16,
    A=4.8, B=1.0, ph=0.7854, ph1=0.7854,
    n1=1, n2=50,
    xi=1.0
)
print(f"Mean kappa = {result['kappa']:.6f}")
print(f"Relative error = {result['Dk']:.4f}")
```

Results are printed to the console as each realisation completes, and optionally saved to a text file (set `save_file=False` to disable).

---

## Computing a single-realisation conductance manually

If you want to inspect individual steps:

### Python

```python
import numpy as np
from vib_transport import (generate_restr_kinks_coord, blocks_coord_def,
                            om_max, transm_vect)
from scipy import integrate

A, B, ph, ph1 = 4.8, 1.0, np.pi/4, np.pi/4
l, k, xi, Ym  = 2, 16, 1.0, 2.0

# 1. Generate a random chain configuration
yzz = generate_restr_kinks_coord(ph, ph1, Ym, l, k)

# 2. Build Hessian and extract blocks
yz  = blocks_coord_def(yzz["CoordExt"], A, B, ph, xi)
H, Vl, Vr = yz["Hin"], yz["Vl"], yz["Vr"]

# 3. Find the upper frequency limit
Om = om_max(A, B, ph)

# 4. Integrate transmission over the band
II, err = integrate.quad(
    lambda x: float(transm_vect(H, Vl, Vr, A, B, ph, np.array([x]))[0]),
    1e-6, Om,
    limit=200, epsrel=2.5e-3, epsabs=1e-3 / (k * l)
)
kappa_one = II * k * l
print(f"Single-realisation kappa·kl = {kappa_one:.6f}")
```

---

## Normalisation and validity checks

After every call to `transmission` (Python) or `Transmission` (MATLAB), check that `Tst` is close to zero:

```python
# Python
T_total, Tst, Trl, Trt, z = transmission(om, Vl, Vr, H, A, B, ph)
print(f"Normalisation check: {Tst}")   # should be ≈ [0, 0]
```

```matlab
% MATLAB
[T, Tst, Trl, Trt] = Transmission(om, Vl, Vr, H, A, B, ph);
disp(Tst)   % should be ≈ [0, 0]
```

If `|Tst| > 0.1`, the frequency is too close to a band edge or the scattering region size `n` is too small. Remedy: increase `n` or shift the frequency away from the band edge.

---

## Installation and requirements

### Python

No installation is required. Copy the `vib_transport/` folder into your working directory.

**Dependencies:** Python 3.8+, NumPy, SciPy. Matplotlib is optional (for plots).

```bash
pip install numpy scipy matplotlib
```

Run the smoke test to verify everything works:

```bash
cd /path/to/parent/of/vib_transport/
python3 vib_transport/example.py
```

### MATLAB

Place all `.m` files from Codes1 and Codes2 on the MATLAB path and call the top-level functions directly. The Statistics and Machine Learning Toolbox is required for `poissrnd`. Parallel Computing Toolbox is used by `parfor` in `TransmVect.m` but is not required — MATLAB falls back to a serial loop automatically if the toolbox is absent.
