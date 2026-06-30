# BreathSense
This document outlines the methodological progression of the BreathSense classification pipeline. It details the transition from traditional, distance-based machine learning paradigms to a non-parametric, multi-state Multiplex Graph architecture, driven by the unique complexities of physiological state detection.

---

## 1. The Core Theoretical Challenge: The "Overlap Problem"

The fundamental roadblock in non-invasive physiological phenotype classification (categorizing individuals as Normal, Gym-Regular, or Athlete) is the **Physiological Overlap Problem**.

During low-stress baseline states, such as resting or walking, the extracted respiratory feature vectors (e.g., waveform skewness, dominant frequency bandwidth) of highly conditioned athletes and sedentary individuals are statistically indistinguishable. If $x$ represents the $N$-dimensional feature vector of a participant, the absolute inter-class Euclidean distance approaches zero during these states:

$$||x_{athlete} - x_{normal}||_2 \approx 0$$

Treating participants as isolated, absolute numerical vectors results in a highly entangled feature space. Standard machine learning algorithms inherently assume independent and identically distributed (I.I.D.) data and attempt to draw absolute spatial boundaries. In this domain, natural intra-class variance frequently eclipses inter-class variance, mathematically guaranteeing the failure of conventional spatial separation.

---

## 2. Phase 1: Absolute Metric Evaluation (The Baseline Failures)

Initial empirical benchmarking evaluated standard architectures that operate on absolute feature magnitudes.

### 2.1 Deep Clustering (1D CNNs & Deep Autoencoders)
* **Methodology:** Raw DR-MUSIC phase signals were mapped into low-dimensional latent embeddings for unsupervised clustering.
* **Performance:** ~60% Accuracy | $F_1$ Score: 0.53
* **Failure Mechanism:** These models evaluated absolute metrics in a vacuum. By evaluating isolated data points, the models could not resolve the overlapping feature space in $\mathbb{R}^5$ during low-exertion activities.

### 2.2 Time-Series Foundation Models
* **Methodology:** Leveraged zero-shot sequence modeling using pre-trained state-of-the-art models (Google's TimesFM and Amazon's Chronos).
* **Performance:** ~55% Accuracy | $F_1$ Score: 0.48
* **Failure Mechanism:** While these models are exceptional at mapping long-range temporal dependencies and sequence patterns, they failed to account for structural physiological entanglement. The problem was not temporal prediction, but spatial overlap.

---

## 3. Phase 2: Transitioning to Differential and Relational Approaches

Recognizing the failure of absolute metrics, the pipeline shifted toward evaluating *changes* and *relationships*.

### 3.1 Differential Matrix Formulation (Delta Vectors)
* **Methodology:** A Delta Vector Matrix was constructed to subtract the resting baseline from exertion states, attempting to capture the true physiological "delta" (reaction to stress). This was followed by Spectral Clustering and HDBSCAN.
* **Performance:** ~80% Accuracy | $F_1$ Score: 0.65
* **Assessment:** While this significantly improved state separation—proving that physiological *change* is a more reliable biomarker than absolute state—it remained insufficient for total class separation.

### 3.2 Parametric Relational Modeling via Graph Neural Networks (GNNs)
* **Methodology:** The system transitioned from Euclidean space to non-Euclidean manifold learning. Participants were modeled as nodes, with edges defined by the Cosine Similarity of their autonomic stress responses. A GNN utilized localized spectral filters to iteratively update node representations by aggregating features from structural neighbors.
* **Performance:** ~75% Accuracy
* **Failure Mechanism:** While theoretically sound, the GNN suffered from parametric complexity. The mathematical overhead of training dense weight matrices ($W^{(l)}$) via backpropagation on a highly localized 72-participant dataset resulted in suboptimal convergence.

---

## 4. Phase 3: The Algorithmic Pivot to Multi-State Superposition

To resolve the parametric bloat of the GNN and prevent single-state graph topologies from collapsing into inseparable "giant components", the architecture underwent a final algorithmic pivot to a non-parametric multiplex topology.

### 4.1 Multiplex Graph Construction
Rather than relying on a single state, the system constructs a unified Multiplex Architecture ($\mathcal{M}$) composed of four independent activity layers: Resting, Walking, Running, and Climbing Stairs.

### 4.2 Boolean Consensus Filtering
To extract only the most robust, latent physiological connections, each continuous-weighted adjacency matrix is passed through a strict similarity threshold ($\tau$) to generate a Boolean matrix ($B^{(k)}$). 

The topological consensus dictates that an edge is preserved if and only if high structural alignment persists across *all* physical layers. This is formulated mathematically as the Hadamard product of the Boolean matrices:

$$B_{final} = \bigoplus_{k=1}^{K} B^{(k)}$$

**Impact:** This strict spatial filter actively dissolves the "Overlap Problem." It strips away transient, exertion-dependent environmental noise, leaving an isolated, clean structural manifold that represents true cardiovascular conditioning.

### 4.3 Automated Labeling via Semi-Supervised Transduction
By utilizing the mathematically lightweight Label Spreading algorithm over the consensus-filtered multiplex graph, the system eliminates the need for deep neural network training. Verified labels from anchor nodes propagate as probability matrices exclusively along the highest-weighted multiplex edges. Unlabeled nodes are classified based on the dominant phenotype of their closest physiological neighbors.

---

## 5. Summary of Achievements
The transition to the non-parametric Multiplex Graph architecture fundamentally solved the physiological overlap bottleneck. By enforcing a multi-state superposition and structural consensus filtering, the final model achieved:
* **State Separation Accuracy:** ~88%
* **$F_1$ Score:** 0.75

This topological approach successfully bridges the gap between raw spatial radar phase data and accurate, automated human lifestyle classification, completely bypassing the limitations of traditional Euclidean machine learning.
