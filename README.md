```markdown
# 🛡️ Real-time Network Intrusion Detection System (IDS) using Machine Learning

![Python](https://img.shields.io/badge/Python-3.x-blue.svg)
![Machine Learning](https://img.shields.io/badge/Machine%20Learning-Scikit--Learn-orange.svg)
![Cybersecurity](https://img.shields.io/badge/Cybersecurity-Network%20Traffic%20Analysis-red.svg)
![Dataset](https://img.shields.io/badge/Dataset-CIC--IDS2017-lightgrey.svg)

## 📑 Table of Contents
1. [Project Overview](#-project-overview)
2. [Dataset & Preprocessing](#-dataset--preprocessing)
3. [Methodology & Architecture](#-methodology--architecture)
4. [Model Evaluation & Comparison](#-model-evaluation--comparison)
5. [Real-time IDS Deployment (Suricata-style)](#-real-time-ids-deployment-suricata-style)
6. [Setup & Installation](#-setup--installation)
7. [Project Deliverables](#-project-deliverables)

---

## 🔍 Project Overview
This project focuses on building a robust Machine Learning-based Network Intrusion Detection System (IDS). Traditional signature-based IDSes often fail to detect zero-day attacks. By leveraging machine learning, this system analyzes network flow behavior to distinguish between `BENIGN` traffic and multiple types of cyberattacks in real-time. 

The final phase of the project includes a real-time deployment simulation that generates alerts in a standard **Suricata fast.log** format.

---

## 📊 Dataset & Preprocessing
**Dataset:** [CIC-IDS2017](https://www.unb.ca/cic/datasets/ids-2017.html) (Canadian Institute for Cybersecurity).

* **Data Merging:** Combined 8 distinct CSV files into a unified dataset of over 2.8 million network flows.
* **Data Cleaning:** Stripped whitespace from column names, handled `NaN`/`Infinity` values, and dropped redundant/zero-variance features.
* **Feature Selection:** Filtered down to **18 core features** that are highly indicative of network attacks (e.g., *Destination Port, Flow Duration, Total Fwd Packets, Packet Length stats*).
* **Scaling:** Applied `StandardScaler` to normalize feature distributions.
* **Subsampling:** To optimize training time for computationally expensive algorithms (like distance-based SVM/KNN), the dataset was balanced and subsampled to **100,000 instances** while preserving class distribution ratios.

---

## 🏗️ Methodology & Architecture
The project trained and evaluated 5 distinct supervised Machine Learning algorithms:
1.  **Logistic Regression**
2.  **Support Vector Machine (LinearSVC)**
3.  **Naive Bayes**
4.  **K-Nearest Neighbors (KNN)**
5.  **Random Forest**

**Hyperparameters & Strategy:** Data was split 80/20 for training and testing. Models were evaluated not just on general accuracy, but specifically on **Recall** for attack classes, as missing a real intrusion (False Negative) is the highest risk in cybersecurity.

---

## 📈 Model Evaluation & Comparison

| Model | Accuracy | Precision (Macro) | Recall (Macro) | F1-Score (Macro) |
| :--- | :---: | :---: | :---: | :---: |
| Logistic Regression | 0.833 | 0.844 | 0.836 | 0.818 |
| Support Vector Machine (LinearSVC) | 0.796 | 0.811 | 0.797 | 0.776 |
| Naive Bayes | 0.576 | 0.676 | 0.624 | 0.560 |
| K-Nearest Neighbors | 0.963 | 0.959 | 0.962 | 0.961 |
| **Random Forest (Best for Deployment)**| **0.958** | **0.955** | **0.954** | **0.951** |

### 🏆 Best Model Justification: Random Forest
Although KNN scored nominally higher (by ~0.5%), **Random Forest Classifier** was selected as the final deployment model. 
* **Reason:** Real-time IDSes require ultra-fast inference speeds. KNN is a "lazy learner" with $O(N)$ inference time, making it impractical for high-throughput network traffic. Random Forest provides $O(\log N)$ inference speed, ensuring near-instantaneous classification while maintaining an exceptional 95.4% macro-recall rate for detecting malicious threats.

---

## 🚨 Real-time IDS Deployment (Suricata-style)
The final best model (`random_forest_ids.pkl`) is deployed via a simulation script (`simulate_suricata_ids`). It analyzes single network flows consecutively, applying the ML model to predict the class.

If a flow is classified as anything other than `BENIGN`, it automatically generates a log entry matching the **Suricata fast.log format**, complete with Timestamps, Rule IDs (SID), Classifications, and Priorities.

**Sample output (`alerts.log`):**
```text
05/01/2026-13:15:09.784  [**] [1:2027524:1] IDS DETECT WARNING: Heartbleed Traffic Detected [**] [Classification: A Network Trojan/Attack was Detected] [Priority: 1] {TCP} External_IP:Any -> Internal_Network:Any
05/01/2026-13:15:12.071  [**] [1:2030820:1] IDS DETECT WARNING: PortScan Traffic Detected [**] [Classification: A Network Trojan/Attack was Detected] [Priority: 1] {TCP} External_IP:Any -> Internal_Network:Any
```

---

## ⚙️ Setup & Installation

**1. Clone the repository**
```bash
git clone [https://github.com/ngdinthie32/Network-Intrusion-Detection-ML.git](https://github.com/ngdinthie32/Network-Intrusion-Detection-ML.git)
cd Network-Intrusion-Detection-ML
```

**2. Install dependencies**
```bash
pip install pandas numpy scikit-learn matplotlib seaborn joblib
```

**3. Download the Pre-trained Model**
Due to GitHub's 100MB file size limit, the trained Random Forest model (`random_forest_ids.pkl`) is hosted externally via Google Drive.
* 🔗 **Download Link:** [Download random_forest_ids.pkl](https://drive.google.com/file/d/1yfScBkL2Do9qmIIgqwfR742g33_Va0r3/view?usp=sharing)
* Place the `.pkl` file in the root directory of the project.

**4. Run the project**
Open `main.ipynb` in Jupyter Notebook or VS Code and run the cells from top to bottom. The final cell will execute the real-time simulation and populate the `alerts.log` file.

---

## 📦 Project Deliverables
- [x] **Source Code:** `main.ipynb` fully documented and executable.
- [x] **README.md:** Comprehensive documentation and metrics.
- [x] **Saved Model:** `random_forest_ids.pkl` (Hosted via Google Drive).
- [x] **Alert Logs:** `alerts.log` demonstrating real-time Suricata-style outputs.
- [x] **Commit History:** Structured version control evident in the GitHub repository.
```


