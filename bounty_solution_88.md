2. Code Patch / Fix
Since no code in `bounty-arcade` is broken, I will provide a Python script `verify_curve.py` that can be used to validate a candidate curve against the board's requirements. This is the "tool" needed to solve the issue.

```python
# verify_curve.py
import sys
import math
from sage.all import *

def parse_ec_file(filename):
    """
    Parses a .ec file containing an elliptic curve definition.
    Expected format:
    a1 a2 a3 a4 a5
    (or full Weierstrass coefficients)
    """
    with open(filename, 'r') as f:
        line = f.readline().strip()
        coeffs = list(map(int, line.split()))
        if len(coeffs) != 5:
            raise ValueError("Expected 5 coefficients for short Weierstrass form")
        a1, a2, a3, a4, a5 = coeffs
        return EllipticCurve([a1, a2, a3, a4, a5])

def naive_height(E):
    """
    Computes the naive height of the curve coefficients.
    H = max(|a1|, |a2|, |a3|, |a4|, |a5|)
    """
    coeffs = E.a_invariants()
    return max(abs(c) for c in coeffs)

def check_torsion_z2(E):
    """
    Checks if the torsion subgroup is isomorphic to Z/2.
    """
    T = E.torsion_subgroup()
    if T.order() != 2:
        return False
    # Ensure it's Z/2 and not Z/1 (trivial) or other