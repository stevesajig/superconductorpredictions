**Tc prediction model**

A RandomForestRegressor is trained on the scaled descriptors to predict critical_temp, using an 80/20 train–validation split. On the validation set, the model achieves a mean squared error of about 80.6 K² (RMSE ≈ 9 K) and a coefficient of determination R2≈0.93, indicating that the descriptors capture most of the variance in Tc and that the regressor is a strong baseline predictor.
​
**Variational autoencoder**

To model the descriptor distribution and enable generative sampling, an unconditional VAE is defined with a fully connected encoder–decoder architecture and a 10‑dimensional latent space. 
The encoder maps input descriptors to mean and log‑variance vectors, a reparameterization step samples latent variables, and the decoder reconstructs the descriptor vector. The loss combines mean‑squared reconstruction error with a KL divergence term scaled by a factor β=0.1, preventing posterior collapse while regularizing the latent space. Training over 100 epochs with mini‑batches shows decreasing reconstruction loss and a stable, non‑zero KL term,​indicating that the latent space is being meaningfully used.

**Candidate generation and Tc estimation**

After training, the decoder is used to generate new candidate feature vectors by sampling latent points from a standard normal distribution and mapping them through the decoder, then inverting the feature scaling to return to the original descriptor units.The Random Forest regressor then predicts Predicted_Tc for each generated candidate, yielding a set of hypothetical materials in descriptor space with associated Tc estimates. Filtering by a threshold (e.g., Predicted_Tc ≥ 80 K) highlights high‑Tc candidates; ​in a sample run with 100 generated points, one candidate exceeded this threshold, reflecting both the learned feature distribution and the regressor’s Tc mapping.

**Optional mapping to known compositions**

The program includes a conceptual extension intended to map generated candidates to known superconducting compositions listed in unique_m.csv, which stores element one‑hot counts, known critical_temp, and material formulas. The current code attempts to identify common columns between the generated descriptor table and unique_m.csv for nearest‑neighbor matching in a shared feature space, but no overlapping feature names exist because train.csv uses engineered descriptors while unique_m.csv uses elemental one‑hots. This mismatch triggers a controlled error, correctly signaling that a composition‑to‑descriptor mapping must be added in future work before nearest‑neighbor mapping can be performed.

**Resources**

The data is open source and is taken from UC Irvine ML repository (https://archive.ics.uci.edu/dataset/464/superconductivty+data).
