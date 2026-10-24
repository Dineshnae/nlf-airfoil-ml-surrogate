# Aerodynamic surrogate: airfoil coefficients from a neural network

**Multilayer perceptron trained on panel-method data · 612 samples · R² 0.998 · MAPE 2.9 % · about 10⁶× faster than the solver**

`Python` · `scikit-learn` · `NumPy` · `MATLAB`

## Summary

A surrogate model that predicts airfoil aerodynamic coefficients from section and flow parameters, trained on data generated with a panel-method solver (XFLR5). The point of a surrogate is speed inside a design loop: one evaluation takes microseconds instead of a solver run, so sizing and trade studies can sweep thousands of candidates.

## Results

| Metric | Value |
|---|---|
| Training data | 612 solver samples |
| Coefficient of determination (R²) on held-out data | 0.998 |
| Mean absolute percentage error | 2.9 % |
| Inference speed against the panel-method solver | about 10⁶× faster |

## Method

1. Design of the sample set across section geometry, angle of attack and Reynolds number.
2. Data generation with the panel-method solver; outliers and unconverged points removed.
3. Multilayer perceptron with a held-out test split; hyperparameters chosen on validation error.
4. Error analysis by region of the input space, and a timing benchmark against the solver.

## Status

The write-up and results are here; the training notebook and data are being prepared for release.

## Author

Dinesh Natarajan, aerospace engineer (MSc, University of Salford), Bengaluru. [GitHub profile](https://github.com/Dineshnae) · [LinkedIn](https://linkedin.com/in/dinesh-natarajan-baaa95206)
