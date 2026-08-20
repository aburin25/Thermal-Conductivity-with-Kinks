# Codes1 — MATLAB to Python conversion

This package is a Python/Numpy/SciPy translation of the MATLAB files supplied in `Codes1.zip`. The original README describes `ThermCondKinksRealDefRestr` as evaluating thermal conductivity by integrated transmission for strongly aligned chains, and `ThermCondKinksRealRWDef` for random-walk chains. 

## Requirements

Python 3.10+ with `numpy` and `scipy`.

```bash
pip install numpy scipy
```

## Main functions

```python
from Codes1_python.thermcond import therm_cond_kinks_real_def_restr, therm_cond_kinks_real_rw_def
```

The translated functions preserve the MATLAB parameter order:

```python
therm_cond_kinks_real_def_restr(l, k, A, B, ph, ph1, n1, n2, xi, Ym)
therm_cond_kinks_real_rw_def(l, k, A, B, ph, ph1, n1, n2, xi)
```

The returned dictionary contains `k`, `Dk`, and `samples` (the last item is an additional Python convenience). The source README defines `l`, `k`, `A`, `B`, `ph`, `ph1`, `n1`, `n2`, `xi`, and `Ym` as the model parameters and reports averaged transmission and relative error as the outcome. 

## Notes

- MATLAB's `poissrnd` is implemented with NumPy's Poisson generator.
- MATLAB's `integral` is implemented with `scipy.integrate.quad`.
- MATLAB linear solves (`\\`) use `numpy.linalg.solve`.
- Complex quantities are represented with NumPy complex arrays.
- MATLAB `parfor` in `TransmVect` is implemented as a normal Python loop for portability and reproducibility.
- The conversion preserves the source algorithms rather than redesigning them.

To run the code, first open a terminal and navigate to the folder that contains the "Codes1_python" folder.

1. Start Python by typing:

   python3

2. Import the `thermcond` module by typing:

   import Codes1_python.thermcond as tc

3. Run the program with:

   result = tc.therm_cond_kinks_real_rw_def(2, 12, 4.8, 1.0, 0.7854, 0.61087, 1, 10, 1.0)

4. Display the results by typing:

   print(result)

The output will then be displayed in the terminal.

5. To save output as a text file type

with open("output.txt", "w") as f:
    f.write(str(result))


with open("output1.txt", "w") as f: print(result, file=f) 