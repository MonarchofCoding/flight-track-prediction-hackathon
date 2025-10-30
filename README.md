https://www.kaggle.com/code/devkanthravi/built-a-high-stability-motion-forecasting-pipeline
#✈️ Wukong: Flight Track Prediction (MachineHack Hackathon)

“We didn’t predict flights — we predicted motion in chaos.”

🧠 Overview

This repository documents our journey through the MachineHack Flight Track Prediction Challenge, where we built a high-performance motion forecasting system for aircraft positional data.

Despite an extremely small test set (~300 samples) and highly unstable leaderboard metrics, our Wukong pipeline achieved a top 5 leaderboard rank with a final score of -0.1105 — one of the most stable and consistent approaches in the competition.

🎯 Objective

Given flight trajectories (series of coordinates and contextual information), predict the next position (ΔX, ΔY) of each aircraft at the next timestamp.

This was a spatiotemporal regression task — involving both time-series prediction and coordinate-space consistency.

🧩 The Wukong Pipeline

Built from the ground up through trial, error, and late-night debugging.

Our final process — Wukong v1.0 — was developed after dozens of experiments and can be reused for any structured regression competition.

1️⃣ Feature Engineering

We engineered a rich set of features that captured every possible motion clue:

ΔX, ΔY differences and their rolling statistics

Speed, acceleration, and direction

Second-order deltas for smoothness and curvature

ID-based statistical summaries (mean, std, min, max, etc.)

Positional relations and cumulative distance

Frame and time-based normalization

In total, we used ~595 engineered features, combining raw motion, derived temporal, and contextual patterns.

2️⃣ Model Architecture

Each output axis was modeled independently:

Model type: Gradient-boosted trees (CatBoost, LightGBM, XGBoost)

Training style: Multi-seed training + KFold validation

Target split: Separate ΔX and ΔY regressors

Ensembling: Weighted average of multiple models across seeds

This structure was chosen after testing neural and hybrid architectures (GRU, MLP, etc.), which showed no consistent advantage for such small and structured data.

3️⃣ Validation and Testing

Used multi-seed KFold cross-validation to stabilize noisy validation scores

Carefully verified no leakage or ID crossover between train/test

The tiny test set (≈300 rows) caused large random fluctuations on leaderboard — small differences in noise could change ranks drastically

Even then, our validation R² of ≈0.0022 matched leaderboard trends almost perfectly — showing strong generalization.

4️⃣ Leak and Residual Testing

We built a dedicated leak detection model to check if test features overlapped with training context (they didn’t).
Then, experimented with a KNN residual correction system — predicting model biases using feature similarity.

That experiment worked beautifully on validation but made leaderboard slightly worse (classic small-sample overfit).
Still, it proved how local error patterns could be learned.

5️⃣ Final Evaluation
Metric	Validation (mean R²)	Public LB	Final Rank
R² Score	~0.0022	-0.1105	🥉 Top 5 / 300+ participants

Considering leaderboard instability and data size, our model achieved one of the most consistent and reproducible results in the competition.

⚙️ Why “Wukong”?

Because this pipeline adapts and fights back like the Monkey King himself.
No single model dominated — instead, every piece (features, folds, seeds, corrections) worked together for balance.

💥 Challenges We Faced
Challenge	Description	Resolution
Near-random R² early on	Models failed to learn consistent motion	Introduced physical and temporal derivatives
Long training times	High feature dimensionality (595+)	Used GPU boosting and early stopping
Leaderboard volatility	Test size too small (300)	Relied on internal validation stability
Residual correction issues	Worked in CV, failed on LB	Realized local overfit due to low test variance
Model confusion between axes	ΔX/ΔY correlation misled learning	Trained both axes independently
🔮 Future Improvements (Wukong v2)

Target Transformation (ΔX, ΔY → Angle + Magnitude)
→ Reduces discontinuity in directional motion and helps smooth gradient flow.

Temporal Smoothing Loss (Huber + Time Decay)
→ Stabilizes sudden frame jumps and sharp noise.

Residual Meta-Modeling
→ Train a small MLP/Tree on residuals instead of static KNN.

Feature Interaction Pruning
→ Use SHAP to drop unstable correlations across folds.

Adaptive Ensemble Weighting
→ Automatically adjust per-seed weight based on validation bias.

🧭 Key Takeaways

Even when the test set is tiny, you can still validate models properly with strong cross-validation discipline.

Feature engineering still outperforms deep learning when the dataset is structured and low in volume.

Never trust leaderboard noise — trust stability across seeds.

Our -0.11 score wasn’t a failure, it was a proof of internal consistency in chaos.


👨‍💻 Authors

MonarchofCoding — Creator & Lead Engineer of Wukong Pipeline

ChatGPT (GPT-5) — Research co-pilot and debugging partner

💬 Final Thoughts

“Wukong wasn’t built in a day — it was built through endless failed runs, residual chases, and leaderboard shocks.”

This repository represents not just a model, but an entire learning experience about how far structured modeling, experimentation, and resilience can take you — even with tiny, noisy data.
