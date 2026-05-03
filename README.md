```markdown
# 🛡️ Real-time Network Intrusion Detection System (IDS) using Machine Learning

## 📖 Overview
This repository presents a real-time Intrusion Detection System (IDS) built using **machine learning models** on the [CIC-IDS2017 dataset]https://www.kaggle.com/datasets/chethuhn/network-intrusion-dataset/data, developed by the Canadian Institute for Cybersecurity (CIC).

Traditional signature-based IDSes often fail to detect zero-day attacks. By leveraging machine learning, this system analyzes **network flow behavior** to distinguish between `BENIGN` traffic and multiple types of cyberattacks in real-time.  
The final phase of the project includes a **real-time deployment simulation** that generates alerts in a standard **Suricata fast.log** format.

---

## 📊 Dataset Characteristics
- **Source:** CIC-IDS2017  
- **Traffic Types:** Normal traffic + multiple attack types  
- **Imbalance:** Majority of records labeled as `BENIGN`  
- **Features:** 79 columns (78 numerical + 1 categorical Label)  
- **Network Flow Features:** Flow duration, packet lengths, ports, TCP/UDP flags, bytes per second, packets per second, etc.  

### Preprocessing Steps
- **Data Merging:** Combined 8 CSV files into a unified dataset of ~2.8M flows.  
- **Cleaning:** Removed whitespace in column names, handled `NaN`/`Infinity`, dropped redundant/zero-variance features.  
- **Feature Selection:** Reduced to **18 core features** highly indicative of attacks (e.g., Flow Duration, Total Fwd/Bwd Packets, Packet Length stats, TCP flags).  
- **Scaling:** Applied `StandardScaler` for normalization.  

---

## 🏗️ Methodology & Architecture
Implemented and compared multiple supervised ML models for **binary and multi-class classification**:

1. Logistic Regression  
2. Support Vector Machine (LinearSVC)  
3. Naive Bayes  
4. K-Nearest Neighbors (KNN)  
5. Random Forest Classifier  

**Training Strategy:**  
- Train/Test split: 80/20  
- Evaluation metrics: Accuracy, Precision, Recall, F1-score, Confusion Matrix  
- Special focus on **Recall for attack classes** (to minimize False Negatives).  

---

## 📈 Model Evaluation & Comparison

| Model | Accuracy | Precision (Macro) | Recall (Macro) | F1-Score (Macro) |
| :--- | :---: | :---: | :---: | :---: |
| Logistic Regression | 0.833 | 0.844 | 0.836 | 0.818 |
| Support Vector Machine (LinearSVC) | 0.796 | 0.811 | 0.797 | 0.776 |
| Naive Bayes | 0.576 | 0.676 | 0.624 | 0.560 |
| K-Nearest Neighbors | 0.963 | 0.959 | 0.962 | 0.961 |
| **Random Forest (Best for Deployment)** | **0.958** | **0.955** | **0.954** | **0.951** |

### Best Model Justification
Although KNN achieved slightly higher accuracy, **Random Forest Classifier** was chosen for deployment because:
- **Inference Speed:** KNN has $O(N)$ inference time (impractical for real-time IDS).  
- **Random Forest:** Provides $O(\log N)$ inference speed, ensuring near-instant classification.  
- **Performance:** Maintains excellent 95.4% recall for malicious traffic detection.  

---

## Real-time IDS Deployment (Suricata-style)
The final model (`random_forest_ids.pkl`) is deployed via a simulation script (`simulate_suricata_ids`).  
It analyzes single network flows consecutively, predicting the class and generating alerts in **Suricata fast.log format**.

**Sample output (`alerts.log`):**
```text
05/01/2026-13:15:09.784  [**] [1:2027524:1] IDS DETECT WARNING: Heartbleed Traffic Detected [**] [Classification: A Network Trojan/Attack was Detected] [Priority: 1] {TCP} External_IP:Any -> Internal_Network:Any
05/01/2026-13:15:12.071  [**] [1:2030820:1] IDS DETECT WARNING: PortScan Traffic Detected [**] [Classification: A Network Trojan/Attack was Detected] [Priority: 1] {TCP} External_IP:Any -> Internal_Network:Any
```

---

## Setup & Installation
1. **Clone the repository**
   ```bash
   git clone https://github.com/ngdinthie32/Network-Intrusion-Detection-ML.git
   cd Network-Intrusion-Detection-ML
   ```
2. **Install dependencies**
   ```bash
   pip install pandas numpy scikit-learn matplotlib seaborn joblib
   ```
3. **Download Pre-trained Model**  
   Due to GitHub’s 100MB limit, the trained Random Forest model is hosted externally.  
   Download `random_forest_ids.pkl` and place it in the project root.  

4. **Run the project**  
   Open `main.ipynb` in Jupyter Notebook or VS Code and execute cells sequentially.  
   The final cell runs the real-time simulation and populates `alerts.log`.  

---

## Project Deliverables
- **Source Code:** `main.ipynb` (fully documented and executable).  
- **README.md:** Comprehensive documentation and metrics.  
- **Saved Model:** `random_forest_ids.pkl` (hosted externally).  
- **Alert Logs:** `alerts.log` with Suricata-style outputs.  
- **Commit History:** Structured version control in GitHub repository.  
```
