# Machine-learning surrogate for natural-laminar-flow airfoil performance

**Neural network predicting Cl, Cd and L/D from operating conditions and geometry · 612 operating points · L/D R² 0.998, MAPE 2.9 % on held-out data · about 10⁶× faster than the viscous-panel solver**

`Python` · `scikit-learn 1.8` · `NumPy` · `XFLR5 (XFoil)`

[Technical report (PDF)](./ML_Surrogate_Technical_Report.pdf)

## Summary

A viscous solve for one airfoil operating point in XFLR5 takes seconds; a design sweep multiplies that by thousands. This project trains a compact surrogate that returns the lift and drag coefficients in about a microsecond, derives L/D from them so the ratio is always consistent with its own lift and drag, and validates it the way a production surrogate would be validated: on a held-out split and on Reynolds numbers it never saw.

The case study compares a conventional NACA 2412 with the natural-laminar-flow NACA 63-215, which reaches a higher peak L/D and a lower drag bucket at the cost of a slightly lower maximum lift and a sharper stall.

## Data

| Parameter | Value |
|---|---|
| Airfoils | NACA 2412 (conventional), NACA 63-215 (natural laminar flow) |
| Angle of attack | −5° to +20° in 0.5° steps |
| Reynolds numbers | 5×10⁵, 7.5×10⁵, 1×10⁶, 1.5×10⁶, 2×10⁶, 3×10⁶ |
| Operating points | 612 |
| Inputs | angle of attack, Reynolds number, camber, camber position, thickness, laminar flag |
| Targets | Cl and Cd; L/D derived as Cl/Cd |

**Data provenance.** The dataset is physically calibrated to reproduce the headline metrics of the companion [NLF airfoil study in XFLR5](https://github.com/Dineshnae/nlf-airfoil-cfd-xflr5) (peak L/D, cruise drag, stall angle and Reynolds scaling), so that the complete pipeline runs end to end and the figures are representative. The production workflow replaces it with a direct XFLR5 batch export in the same schema; every downstream step is unchanged. Absolute coefficient values should be regenerated from real solver data before quantitative use.

## Models and validation

Four regressors were benchmarked on a random 80/20 hold-out split, then the chosen model was tested leave-one-Reynolds-out: train on five Reynolds numbers, predict the sixth.

| Model | L/D R² | L/D MAPE | Cd R² |
|---|---|---|---|
| Linear regression | −1.58 | 108 % | 0.664 |
| Random forest | 0.963 | 8.2 % | 0.985 |
| Gradient boosting | 0.995 | 2.5 % | 0.999 |
| **Neural network (MLP)** | **0.998** | **2.9 %** | **0.999** |

| Held-out Reynolds number | L/D MAPE |
|---|---|
| 5×10⁵ | 6.7 % |
| 7.5×10⁵ | 2.7 % |
| 1×10⁶ | 6.7 % |
| 1.5×10⁶ | 3.4 % |
| 2×10⁶ | 4.7 % |
| 3×10⁶ (extrapolation) | 10.8 % |

The surrogate interpolates across the Reynolds range within a few percent and degrades at the extrapolation boundary, as it should. Inference takes about one microsecond per point against seconds per point for the solver.

**Final network:** hidden layers 64–64–32, ReLU, Adam at learning rate 2×10⁻³, L2 alpha 1×10⁻⁴, early stopping with patience 50, standardised inputs and outputs.

## Limits and next steps

- Two airfoils: the model interpolates across operating conditions, not across arbitrary geometry.
- Accuracy falls outside the trained Reynolds envelope; query inside it or retrain with wider coverage.
- Roadmap: a physics-informed variant, training across a parametrised airfoil family for shape optimisation, and coupling to an optimiser for inverse design.

## Files

| File | Contents |
|---|---|
| `ML_Surrogate_Technical_Report.pdf` | Full report: data definition, model benchmark, parity and generalisation plots, hyperparameters, references |

The training pipeline, model class, notebook and saved model are being added to this repository.

## Author

Dinesh Natarajan, aerospace engineer (MSc, University of Salford), Bengaluru. [GitHub profile](https://github.com/Dineshnae) · [LinkedIn](https://linkedin.com/in/dinesh-natarajan-baaa95206)
