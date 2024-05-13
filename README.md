# MadeWithML

1. 🎨 Desing 
   - Setup 

Cluster 
---
I have a **head node** that manages the cluster. Also it will be connected to a set of **worker nodes** that will execute workloads for us. 

![ray](https://docs.ray.io/en/latest/_images/ray-cluster.svg)


Envoirenment
---

I can use a tool pyenv-win (windows) to easily download and switch between Python version. Recomended python version is 3.10

```
pip install pyenv-win
pyenv install 3.10.11
pyenv global 3.10.11
```

Create a virtual envoirenment to install our dependencies.

```
mkdir makewithml
cd makewithml
python3 -m venv venv  # create virtual environment
source venv/bin/activate  # on Windows: venv\Scripts\activate
python3 -m pip install --upgrade pip setuptools wheel
```

Compute
---
My personel Laptop will act as the cluster.
1 CPU --> Head node
Remainin nodes --> Worker nodes

Workspaces
---

optional : Anyscale workspace
![workspaces](https://madewithml.com/static/images/mlops/setup/workspaces.png)

Git
---

```
export GITHUB_USERNAME="AhmetEnesYalcinkaya" #YOUR_GITHUB_UESRNAME
git clone https://github.com/GokuMohandas/Made-With-ML.git .
git remote set-url origin https://github.com/$GITHUB_USERNAME/Made-With-ML.git
git checkout -b dev
export PYTHONPATH=$PYTHONPATH:$PWD  # so we can import modules from our scripts

python -m pip install -r requirements.txt
```
---

- Product Design

Product design (What & Why) → Systems design (How)

![design](https://madewithml.com/static/images/mlops/design/ml_canvas.png)

**Background** : 
- users: machine learning developers and researchers.
- goals: stay up-to-date on ML content for work, knowledge, etc.
- pains: too much unlabeled content scattered around the internet.

**Value proposition**

- product: a service that discovers and categorizes ML content from popular sources.
- alleviates: display categorized content for users to discover.
- advantages: when users visit our platform to stay up-to-date on ML content, they don't waste time searching for that content themselves in the noisy internet.

![design](https://madewithml.com/static/images/mlops/design/product.png)

**Objectives**: 

*Our task*

- Discover ML content from trusted sources to bring into our platform.
- Classify incoming content for our users to easily discover. [OUR FOCUS]
- Display categorized content on our platform (recent, popular, recommended, etc.)

**Solution**

*Core features*

- predict the correct tag for a given content. [OUR FOCUS]
- user feedback process for incorrectly classified content.
- workflows to categorize ML content that our model is incorrect / unsure about.
  
*Integrations*

- ML content from reliable sources will be sent to our service for classification.
  
*Alternatives*

- allow users to add content manually and classify them (noisy, cold start, etc.)
  
*Constraints*

- maintain low latency (>100ms) when classifying incoming content. [Latency]
- only recommend tags from our list of approved tags. [Security]
- avoid duplicate content from being added to the platform. [UI/UX]

*Out-of-scope*

- identify relevant tags beyond our approved list of tags (natural-language-processing, computer-vision, mlops and other).
- using full-text HTML from content links to aid in classification.

**Feasibility**

We have a dataset with ML content that has been labeled. We'll need to assess if it has the necessary signals to meet our objectives.

Sample data point

```json
{
    "id": 443,
    "created_on": "2020-04-10 17:51:39",
    "title": "AllenNLP Interpret",
    "description": "A Framework for Explaining Predictions of NLP Models",
    "tag": "natural-language-processing"
}
```


- System Design

![systemdesign](https://madewithml.com/static/images/mlops/systems-design/workloads.png)

**Data**

Describe the training and production (batches/streams) sources of data

```
id	created_on	title	description	tag
0	6	2020-02-20 06:43:18	Comparison between YOLO and RCNN on real world ...	Bringing theory to experiment is cool. We can ...	computer-vision
1	89	2020-03-20 18:17:31	Rethinking Batch Normalization in Transformers	We found that NLP batch statistics exhibit large ...	natural-language-processing
2	1274	2020-06-10 05:21:00	Getting Machine Learning to Production	Machine learning is hard and there are a lot, a lot of ...	mlops
4	19	2020-03-03 13:54:31	Diffusion to Vector	Reference implementation of Diffusion2Vec ...	other
```

*training*
- access to training data and testing (holdout) data.
- was there sampling of any kind applied to create this dataset?
- are we introducing any data leaks?
 
*production*
- access to batches or real-time streams of ML content from various sources
- how can we trust that this stream only has data that is consistent with what we have historically seen?

**Labeling**

![labeling](https://madewithml.com/static/images/mlops/labeling/workflow.png)

*Assumption:* Content can only belong to one category (multiclass).	
*Reality:* Content can belong to more than one category (multilabel).	
*Reason:* For simplicity and many libraries don't support or complicate multilabel scenarios.

**Metrics**

For our task, we want to have both high precision and recall, so we'll optimize for f1 score (weighted combination of precision and recall). We'll determine these metrics for the overall dataset, as well as specific classes or slices of data.

- True positives (TP): we correctly predicted class X.
- False positives (FP): we incorrectly predicted class X but it was another class.
- True negatives (TN): we correctly predicted that it's wasn't the class X.
- False negatives (FN): we incorrectly predicted that it wasn't the class X but it was.

![metrics](https://madewithml.com/static/images/mlops/evaluation/metrics.png)

Bir kaynak bozulma problemi için

kaynak bozukluk tahmini için yaptığım 100 tahminde ; 
Precision : kaynak bozuk demişim bozuk çıkmış / ( kaynak bozuk demişim bozuk çıkmış + kaynak bozuk demişim ama yanlış bilmişim bozuk değilmiş )

Recall :  kaynak bozuk demişim bozuk çıkmış / ( kaynak bozuk demişim bozuk çıkmış + kaynak bozuk değiş demişim bozuk çıkmış )

**Evaluation**

Offline evaluation : Evaluate on test data
Online evaluation : manually label a subset of incoming data to evaluate periodically.
asking the initial set of users viewing a newly categorized content if it's correctly classified.
allow users to report misclassified content by our model.

**Modelling** 

*End to end utility*
*Manuale before ML*
*Augment vs. automate*
*Internal vs. external*
*Thorough*

**Inference**
 we need to think about whether we want to perform batch (offline) or real-time (online) inference.

**Feedback**

![feedback](https://madewithml.com/static/images/mlops/systems-design/development_cycle.png)
