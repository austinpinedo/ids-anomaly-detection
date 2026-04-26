# Network Intrusion Detection: Can Clustering Separate Bot from Benign Traffic Without Labels?

Most intrusion detection systems are built with supervised classifiers that need labeled traffic to learn from. The problem is that new attack types show up all the time that weren't in the training set, so you need a way to catch stuff that doesn't look like anything you've seen before. This project asks whether unsupervised clustering can do that. Specifically, whether KMeans or DBSCAN can find the bot/benign split in CIC-IDS-2018 without ever seeing the labels.

👉 **Start here: [`notebooks/main_notebook.ipynb`](notebooks/main_notebook.ipynb)**

🎥 **Project video:** [Watch on YouTube](https://www.youtube.com/watch?v=UEIP4hD9rFo)

## Research Question

Can KMeans and DBSCAN naturally separate bot from benign traffic without using labels, and do the resulting clusters line up with the true class distribution?

## Results Summary

**Short answer: no, neither one recovers the bot/benign boundary.** Across KMeans runs from k=2 to k=8, Adjusted Rand Index landed between -0.09 and -0.002, so at best uncorrelated with the true labels and at worst slightly anti-correlated. Silhouette scores climbed from 0.45 to 0.74 as k went up, which means the algorithms *are* finding real structure in the data. It's just not the structure that maps to "bot vs benign." DBSCAN's noise points showed a more interesting pattern that the notebook digs into. Full analysis, plots, and what I think it all means are in `main_notebook.ipynb`.

## Dataset

**CIC-IDS-2018** from the Canadian Institute for Cybersecurity, a flow-based intrusion detection benchmark. I'm using the Friday, February 3rd capture, which has:

- 1,048,575 network flow records
- 80 features per flow (packet counts, byte volumes, inter-arrival times, etc.)
- Binary ground truth: Benign (~73%) and Bot (~27%)

The full dataset is around 6GB and isn't committed to the repo. To reproduce, grab the Friday Feb 3rd CSV from the [CIC-IDS-2018 page](https://www.unb.ca/cic/datasets/ids-2018.html) and drop it in the `datasets/` folder. The notebook expects the path near the top of the "Loading the data" section, so point it at wherever you saved the file.

### Preprocessing

Everything happens inside `main_notebook.ipynb`:

1. Stratified 50k-row sample that keeps the bot/benign ratio
2. Drop non-numeric and non-predictive columns
3. Remove highly correlated features
4. Pick the top 10 features by between-class separation
5. Clip to 1st/99th percentiles, then standardize

## How to Reproduce

This was built locally in VS Code with Python 3.11.9. To re-run:

```bash
# clone the repo
git clone https://github.com/austinpinedo/ids-anomaly-detection.git
cd ids-anomaly-detection

# create a virtual environment 
python -m venv .venv
source .venv/bin/activate    # macOS/Linux
.venv\Scripts\activate       # Windows

# install dependencies
pip install -r requirements.txt

# download the dataset (see Dataset section above) into datasets/

# launch the notebook
jupyter notebook notebooks/main_notebook.ipynb
```

Then run cells top to bottom.

## Key Dependencies

- Python 3.11.9
- pandas
- numpy
- scikit-learn
- matplotlib
- seaborn

Full list with pinned versions is in [`requirements.txt`](requirements.txt).

## Repo Structure

```
ids-anomaly-detection/
├── README.md
├── requirements.txt
├── .gitignore
├── datasets/
│   └── README.md
└── notebooks/
    ├── main_notebook.ipynb
    ├── research_project.ipynb
    └── research_project_2.ipynb
```

## 📎 Author

Austin Pinedo
