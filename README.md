CPU Temperature Forecasting using XGBoost, Random Forest & LightGBM

Overview

This project focuses on developing a high-precision CPU thermal forecasting system capable of predicting processor temperatures 10 seconds into the future. The primary goal was to eliminate the temporal phase lag commonly observed in recurrent neural network approaches such as LSTM and provide accurate, real-time thermal predictions.

---

Problem Statement

Modern CPUs experience rapid thermal fluctuations under varying workloads. Accurate short-term temperature forecasting is essential for:

- Thermal management systems
- Dynamic voltage and frequency scaling (DVFS)
- Preventive cooling strategies
- Hardware reliability and performance optimization

Initial experiments using LSTM models revealed a significant challenge:

Temporal Phase Lag

The predicted temperature curve behaved like a delayed version of the actual temperature signal.

Observed Issues:

- Predictions lagged behind real temperatures by approximately 10–20 seconds
- Sudden thermal spikes were detected only after they occurred
- The model failed to anticipate rapid workload-induced heating

---

Proposed Solution

Instead of directly predicting the future temperature value, the problem was reformulated as a temperature change prediction task.

Delta Forecasting Approach

Traditional target:

y = T(t + h)

Proposed target:

y = T(t + h) - T(t)

Final forecast:

Predicted Temperature = Current Temperature + Predicted Delta

By predicting temperature change (ΔT) rather than absolute temperature, the model became more responsive to workload-driven thermal events.

---
## Dataset

Custom dataset generated through controlled CPU workload simulations and hardware telemetry collection using HWiNFO64.

- 21,816 timestamped records
- Sampling interval: 0.5 seconds
- Multiple CPU workload scenarios
- Real-time thermal and performance telemetry

| Feature | Description |
|----------|-------------|
| temp_cpu | Current CPU temperature |
| util_total | Total CPU utilization |
| power_pkg | CPU package power consumption |
| power_vel | Rate of change of package power |
| util_vel | Rate of change of utilization |
| Additional telemetry features | System monitoring parameters |

---

Models Evaluated

LSTM

- Sequential deep learning model
- Captured trends but suffered from phase lag
- Produced delayed predictions

Random Forest Regressor

- Ensemble tree-based approach
- Improved responsiveness to workload changes
- Reduced prediction lag

LightGBM

- Gradient boosting framework
- Fast training and strong performance
- Effective on engineered features

XGBoost

- Best performing model
- Lowest forecasting error
- Successfully anticipated thermal spikes

---

## Feature Engineering

To improve forecasting accuracy and reduce prediction lag, several derived features were engineered from the raw telemetry data.

### Velocity Features

These features represent the rate of change of key system metrics and act as leading indicators of future thermal behavior.

| Feature | Formula | Purpose |
|----------|----------|----------|
| power_vel | Δ(power_pkg) | Captures rapid changes in CPU power consumption |
| util_vel | Δ(util_total) | Captures changes in CPU workload intensity |

---

Key Findings

1. Elimination of Phase Lag

The Delta forecasting strategy removed the conservative behavior seen in LSTM models and allowed the system to react immediately to workload changes.

2. Importance of Leading Indicators

CPU temperature is a delayed consequence of electrical activity.

The strongest predictors of future thermal behavior were:

- Package Power ("power_pkg")
- Total Utilization ("util_total")
- Power Velocity ("power_vel")
- Utilization Velocity ("util_vel")

3. Feature Importance Analysis

XGBoost gain analysis revealed:

- "temp_cpu" acts as the baseline anchor
- "power_pkg" and "util_total" drive thermal changes
- Velocity features help eliminate phase lag and improve anticipation of thermal spikes

---

## Results

### XGBoost Performance

| Metric | Value |
|---------|---------|
| R² Score | 0.9511 |
| MAE | 1.4875 °C |
| RMSE | 3.5913 °C |

---

Model Comparison

The comparison between Actual Temperature, Random Forest Predictions, and XGBoost Predictions demonstrates:

- Accurate tracking across multiple thermal cycles
- Minimal forecasting lag
- Strong prediction of rapid temperature transitions
- Improved thermal spike anticipation

---

## Project Workflow

```text
CPU Telemetry Data
        ↓
Data Cleaning
        ↓
Feature Engineering
        ↓
Delta Target Generation
        ↓
Train/Test Split
        ↓
Model Training
(XGBoost / RF / LightGBM)
        ↓
Temperature Delta Prediction
        ↓
Final Temperature Reconstruction
```

---

Technologies Used

- Python
- Pandas
- NumPy
- Scikit-Learn
- XGBoost
- LightGBM
- Matplotlib
- Jupyter Notebook

---

Future Improvements

- Multi-step forecasting horizons
- Real-time deployment dashboard
- GPU thermal forecasting
- Online learning for adaptive prediction
- Integration with thermal control systems

---

Conclusion

This project demonstrates that reframing CPU thermal forecasting as a temperature-change prediction problem significantly improves predictive performance. By leveraging ensemble machine learning models and workload-driven leading indicators, the system successfully eliminates temporal phase lag and provides accurate short-term thermal forecasts suitable for real-world thermal management applications.
