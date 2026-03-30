# Berlekamp Algorithm for Polynomial Factorisation over Finite Fields

**Bachelor's Thesis in Mathematics | Universidad de Valladolid (2026)**  
**Author:** David García García  
**Grade: 9/10**

---

## Overview

This repository contains a complete theoretical and computational study of the **Berlekamp algorithm** for factoring polynomials over finite fields $\mathbb{F}_p$, where $p$ is a prime number.

The algorithm has direct applications in:
- **Cryptography** (RSA, elliptic curve cryptography)
- **Error-correcting codes** (Reed-Solomon, BCH codes)
- **Computer algebra systems**

> **Scope:** This implementation works over $\mathbb{F}_p$ for **prime** $p$ only.  
> The algorithm is designed for **square-free** polynomials. If the input has repeated factors — including the special case where $f'(x) = 0$ in characteristic $p$ — the program detects this automatically and factors the square-free part.

---

## Repository Structure

```
Berlekamp_Algorithm_Finite_Fields/
│
├── berlekamp_algorithm.ipynb   # Full implementation and analysis notebook
├── thesis_berlekamp_finite_fields.pdf  # Complete thesis (Spanish)
├── LICENSE                     # MIT License
└── README.md                   # This file
```

---

## Notebook Contents

The notebook is structured in 12 blocks and designed to run **cell by cell** without any manual input — all examples have pre-defined data. An optional interactive cell at the end allows factoring any custom polynomial.

| Block | Description |
|-------|-------------|
| 1 | Complete algorithm implementation (all function definitions) |
| 2–8 | Arithmetic, GCD, modular exponentiation, derivative, square-free extraction |
| 9 | `factorise_and_print` pipeline with zero-derivative case handling |
| 10 | Examples from the thesis + zero-derivative cases |
| 11 | Performance analysis: time vs degree, time vs prime, heatmap |
| 12 | Large-scale examples (degree up to 30, prime up to 37) |
| — | Interactive mode *(optional, run manually)* |

---

## Algorithm Overview

The Berlekamp algorithm factorises a monic square-free polynomial $f(x) \in \mathbb{F}_p[x]$ of degree $n$ as follows:

1. **Build the Berlekamp matrix** $Q$ — an $n \times n$ matrix whose $i$-th column contains the coefficients of $x^{pi} \bmod f(x)$. This encodes the Frobenius automorphism $x \mapsto x^p$ on $\mathbb{F}_p[x]/(f)$.
2. **Compute the kernel** of $Q - I$ via Gauss-Jordan elimination. Its dimension $k$ equals the number of distinct irreducible factors of $f$.
3. **Split the factors** using each kernel vector $h(x)$: compute $\gcd(f, h - c)$ for $c \in \mathbb{F}_p$ until a non-trivial divisor is found.

### Special case: $f'(x) = 0$

In characteristic $p$, the formal derivative $f'(x) = 0$ if and only if every exponent in $f$ is a multiple of $p$. In this case:

$$f(x) = g(x^p) = g(x)^p$$

The implementation extracts $g$ via the Frobenius endomorphism and applies `make_squarefree` recursively, handling arbitrarily nested cases such as $f = h^{p^k}$.

---

## Complexity

| Phase | Cost |
|-------|------|
| Square-free decomposition $\gcd(f, f')$ | $O(n^2)$ |
| Berlekamp matrix construction | $O(n^3)$ |
| Kernel of $B - I$ (Gaussian elimination) | $O(n^3)$ |
| Factor separation via $\gcd(f, h - c)$ | $O(qn^2)$ |

$$T(n, q) = O(n^3) + O(qn^2)$$

The $O(n^3)$ term dominates when $q \ll n$ (e.g. binary BCH codes); the $O(qn^2)$ term dominates when $q \gg n$. In practice, the GCD splitting step terminates early — measured growth in $p$ is sublinear compared to the theoretical worst case.

Berlekamp is **deterministic and conceptually simple**, especially suited for small fields.

---

## Examples

### Standard factorisation in $\mathbb{F}_2[x]$
```python
# f(x) = x^12 + x^9 + x^6 + x^3 + 1
factorise_and_print([1, 0, 0, 1, 0, 0, 1, 0, 0, 1, 0, 0, 1], p=2)
# Result: (x^4 + x + 1) * (x^4 + x^3 + x^2 + x + 1) * (x^4 + x^3 + 1)
```

### Zero-derivative case in $\mathbb{F}_2[x]$
```python
# f(x) = x^4 + 1
# f'(x) = 0  =>  f(x) = g(x^2) = g(x)^2 with g(x) = x^2 + 1 = (x+1)^2
# =>  f(x) = (x+1)^4
factorise_and_print([1, 0, 0, 0, 1], p=2)
# Square-free part: (x + 1)
```

### Large-scale: degree 30 over $\mathbb{F}_{31}$
```python
# Factors a degree-30 polynomial into 7 irreducible factors in ~25ms
```

---

## Interactive Use

Run the last cell of the notebook to factor any polynomial interactively:

```
Degree of the polynomial: 5
Enter 6 coefficients (highest to lowest): 1 0 0 0 0 1
Enter p (prime): 2
```

Coefficients are entered from **highest to lowest** degree.

---

## Tools and Technologies

- **Language:** Python · Markdown · LaTeX
- **Libraries:** `numpy`, `matplotlib`
- **Environment:** Jupyter Notebook · Visual Studio Code
- **Dataset:** No external data required — all polynomials are generated programmatically

---

## About

Developed as a Bachelor's thesis in Mathematics at the Universidad de Valladolid.  
Supervised by Manuel Mariano Carnicer Arribas.

*Full theoretical development, proofs, and complexity analysis available in the thesis PDF (Spanish).*
