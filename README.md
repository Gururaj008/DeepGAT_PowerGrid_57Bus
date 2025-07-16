# Deep GAT Ablation & Early Stopping on IEEE Power Grids

This repository presents an end‑to‑end Graph Neural Network (GNN) pipeline for **node classification** on IEEE test grids. We focus on the 57‑bus system, exploring:

- **Feature ablation** (base vs. voltage angle vs. impedance)  
- **Deep GAT architectures** (varying attention heads & layers)  
- **Early stopping** to avoid overfitting  
- **Quantitative comparison** of test accuracy  

---

## 🔍 Project Overview

1. **Input & Preprocessing**  
   - Load IEEE grids via [`pandapower.networks`](https://pandapower.readthedocs.io/)  
   - Bus features:  
     - Voltage magnitude (`vm_pu`)  
     - Active/reactive power (`p_mw`, `q_mvar`)  
     - Voltage angle (`va_degree`)  
     - Generator flag (`gen_flag`)  
     - Optional per‑node average line impedance  
   - Normalize all features (zero‑mean, unit‑variance)  
   - Build undirected graph: lines + transformers  

2. **Models**  
   - **DeepGAT**: multi‑layer Graph Attention Network  
     - 2–3 layers, 4–16 heads, hidden dim=16, dropout=0.6  
   - **Loss**: weighted cross‑entropy (to balance generator vs. load classes)  
   - **Optimizer**: Adam (lr=0.005, weight_decay=1e‑4)  
   - **Early Stopping**: monitor validation accuracy (patience=20)  

3. **Experiments**  
   - Grid search over:  
     - Feature sets: `base` / `with_angle` / `with_impedance`  
     - Heads: 4, 8, 16  
     - Layers: 2, 3  
   - Train/test splits: 60/20/20%  

4. **Results**  
   - **Best Configurations** (Test Accuracy)  
     | Feature Set   | Heads | Layers | Test Accuracy |
     |--------------:|------:|-------:|--------------:|
     | **base**      | 4     | 3      | **0.917**     |
     | **with_angle**| 16    | 3      | **0.917**     |
     | **with_imp**  | 16    | 3      | 0.833         |

   - ![Best GAT Test Accuracy by Feature Set](images/best_test_accuracy.png)  
     *Base* and *with_angle* both hit ~92 %, while adding impedance degrades performance.

---

