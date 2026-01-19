# ADMET-Challenge-Report-Methodology

Model Report: A Specialist Ensemble Framework with Public Data Augmentation for Clustered ADMET Prediction
1. Summary
This report details the methodology for our final submission, which employs an ensemble of specialist Graph Neural Networks (GNNs). Recognizing that different ADMET properties are governed by distinct biophysical principles, we rejected a monolithic multi-task approach. Instead, we trained separate, optimized models for biologically relevant clusters of ADMET tasks. To maximize generalization and overcome data scarcity, we augmented the provided training data with thousands of curated public data points from the Therapeutics Data Commons (TDC). All models were trained using a custom Censored Tobit-MAE Loss to correctly interpret inequality-based data. The final submission is a "stitched" composite of the predictions from each specialist model.
2. Core Methodology
Task Clustering: The nine ADMET endpoints were grouped into three specialist clusters, allowing each model to focus on a coherent set of properties:
Physics Cluster: LogD, Kinetic Solubility (KSOL)
Metabolism/Permeability Cluster: HLM CLint, MLM CLint, Caco-2 Papp, Caco-2 Efflux
Binding Cluster: MPPB, MBPB, MGMB
Public Data Augmentation: The training set for each specialist was augmented with relevant public datasets from TDC. For example, the Physics cluster was trained on the 5,331 competition molecules plus ~14,000 additional molecules from LogD_Lipophilicity_AstraZeneca and Solubility_AqSolDB. This expanded chemical space forces the model to learn more generalizable structure-property relationships.
Censored Learning: All models were trained using a custom Censored Tobit-MAE Loss function. This allows the model to correctly interpret and learn from inequality-based data (e.g., >325 µM), which is critical for identifying the best-in-class compounds that saturate experimental assays.
3. Architecture and Training
Backbone Architecture: The core architecture for each specialist model was a Hybrid GNN. This combines a 3-layer Graph Convolutional Network (GCN) with a vector of 208 pre-computed RDKit physicochemical descriptors. The fusion of learned graph embeddings with explicit physical features provides both topological awareness and a strong inductive bias.
Validation and Stability: Each specialist model was trained using a 5-Fold Cross-Validation scheme. The final predictions for each task cluster are the arithmetic mean of the 5 out-of-fold models, ensuring maximum stability and reduced variance.
Regularization: We employed Early Stopping with a patience of 10 epochs, monitoring the validation set Spearman correlation to prevent overfitting, which is especially important on the large, augmented datasets.
4. Post-Processing
Final predictions were calibrated by applying Physical Clamping, where values were clipped to a physically realistic range (e.g., protein binding cannot be negative; solubility is capped) derived from the training data distribution. This step is critical for minimizing the Mean Absolute Relative Error (MA-RAE) by preventing the model from predicting extreme, physically impossible outliers.
5. Conclusion
Our final submission is the result of a deliberate strategy to emulate the winning approaches of modern ADMET challenges. By training specialist models on augmented public data and using a mathematically sound loss function for censored values, we created a robust and highly specialized predictive tool. This "stitched ensemble" of experts is designed to provide the most accurate possible predictions for each distinct biological category.
