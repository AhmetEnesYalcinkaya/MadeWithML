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