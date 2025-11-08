# Quick Start Guide - Fourier Cointegration Package (Windows)

## Installation (Choose ONE method)

### Method 1: Direct Install (Easiest)
```cmd
cd C:\path\to\downloaded\package
pip install .
```

### Method 2: Development Mode
```cmd
cd C:\path\to\downloaded\package
pip install -e .
```

### Method 3: From GitHub (After publishing)
```cmd
pip install git+https://github.com/merwanroudane/fouriercoint.git
```

## Verify Installation

```cmd
python -c "from fouriercoint import fourier_cointegration_test; print('✓ Package installed successfully!')"
```

## Your First Test (Copy & Paste This)

Create a file `test_cointegration.py`:

```python
import numpy as np
from fouriercoint import fourier_cointegration_test

# Set random seed for reproducibility
np.random.seed(42)

# Generate sample data (200 observations)
T = 200

# Create I(1) process (random walk)
x = np.cumsum(np.random.randn(T, 1))

# Create cointegrated y with structural break at midpoint
t = np.arange(T)
structural_break = 3 * (t > T//2)  # Level shift
y = 2 + 0.5 * x + structural_break.reshape(-1, 1) + np.random.randn(T, 1)

# Run the test
print("Running Fourier Cointegration Test...")
print("=" * 70)

results = fourier_cointegration_test(
    y=y,
    x=x,
    m=0,              # 0 = constant + Fourier (level shifts)
    kmax=3,           # Test frequencies 1, 2, 3
    significance_level=0.05,
    use_dols=True,    # Use DOLS estimation
    verbose=True      # Print detailed output
)

# Extract key results
print("\n" + "=" * 70)
print("SUMMARY")
print("=" * 70)
print(f"Test Statistic:        {results['test_statistic']:.6f}")
print(f"Critical Value (5%):   {results['critical_value']:.6f}")
print(f"Optimal Frequency:     k* = {results['optimal_k']}")
print(f"Cointegration Found:   {not results['reject_null']}")
print(f"Structural Break:      {results['fourier_significant']}")
```

Run it:
```cmd
python test_cointegration.py
```

## Expected Output

```
======================================================================
FOURIER COINTEGRATION TEST RESULTS
Tsong et al. (2016)
======================================================================
Sample size (T):              200
Number of regressors (p):     1
Model specification (m):      0 (constant + Fourier)
Estimation method:            DOLS
DOLS leads/lags (q):          5
Kernel:                       bartlett
----------------------------------------------------------------------
Optimal Fourier frequency:    k* = 1
Test statistic CI^m_f:        0.034567
Critical value (5%):          0.124000
Long-run variance:            0.089234
----------------------------------------------------------------------
Critical values:
  10%:  0.095000
  5%:   0.124000
  1%:   0.198000
----------------------------------------------------------------------
F-test for Fourier:           F = 12.3456
F critical value (5%):        4.0660
Fourier significant:          True
======================================================================
CONCLUSION: Do not reject the null hypothesis of cointegration at 5.0% level.
Evidence supports cointegration with structural breaks (Fourier frequency k=1).
======================================================================
```

## Common Use Cases

### Case 1: Fiscal Sustainability

```python
import pandas as pd
from fouriercoint import fourier_cointegration_test

# Load your data
data = pd.read_excel('fiscal_data.xlsx')
revenue = data['revenue'].values.reshape(-1, 1)
expenditure = data['expenditure'].values.reshape(-1, 1)

# Test cointegration
results = fourier_cointegration_test(
    y=revenue,
    x=expenditure,
    m=1,  # Include trend
    kmax=3
)

if not results['reject_null']:
    print("✓ Evidence of fiscal sustainability")
else:
    print("✗ No evidence of fiscal sustainability")
```

### Case 2: Multiple Regressors

```python
# With 3 regressors
x_multi = np.cumsum(np.random.randn(200, 3), axis=0)
y = 2 + x_multi @ np.array([[0.5], [0.3], [-0.2]]) + np.random.randn(200, 1)

results = fourier_cointegration_test(y, x_multi, m=1)
```

### Case 3: Custom Options

```python
results = fourier_cointegration_test(
    y=y,
    x=x,
    m=1,                    # Model with trend
    kmax=5,                 # Test more frequencies
    q=3,                    # DOLS leads/lags
    kernel='qs',            # Quadratic Spectral kernel
    bandwidth=10,           # Manual bandwidth
    significance_level=0.01,# 1% significance
    use_dols=True,
    verbose=True
)
```

## Troubleshooting

### Problem: "Module not found"
```cmd
# Reinstall
pip uninstall fouriercoint
pip install C:\path\to\package
```

### Problem: "numpy not found"
```cmd
pip install numpy scipy
```

### Problem: Test statistic too large
- Check your data is actually I(1)
- Ensure you have enough observations (T ≥ 100)
- Try increasing noise level if testing synthetic data

## Need Help?

- GitHub Issues: https://github.com/merwanroudane/fouriercoint/issues
- Email: merwanroudane920@gmail.com
- Documentation: See README.md

## Next Steps

1. Run the test with your own data
2. Refer to `examples.py` for more examples
3. Read COMPATIBILITY_REPORT.md for detailed methodology
4. Check README.md for complete documentation

---

**Package:** fouriercoint v1.0.0  
**Author:** Dr. Merwan Roudane  
**License:** MIT
