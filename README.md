# Hyperparameter Search using Ray Tune & Hugging Face

This repository holds some example code for tuning hyperparameters of a Hugging Face model using Ray, in Domino.

The results are also logged to the Domino experiment manager using MLflow.

## Data Setup

When using on-demand Ray clusters in Domino, the data needs to be on an external storage so that it can be globally accessible by all the workers. The best way to do this is by using [Domino Datasets](https://docs.dominodatalab.com/en/5.1/user_guide/0a8d11/datasets/).

The dataset that is used for this example is included in this repository (`data.csv`). Upload a copy of the file into a Domino Dataset.

## Environment Setup

The example requires two Domino environments to be built - one for the workspace and one for the cluster.

### Workspace Environment

Base image: `quay.io/domino/compute-environment-images:ubuntu20-py3.9-r4.3-domino5.7-standard`

Additional Dockerfile instructions:

```
RUN pip install datasets==2.12.0 transformers==4.33.1 torch==2.0.1 ray[all]==2.6.3 accelerate==0.23.0 streamlit==1.26.0 tblib==2.0.0 pandas==2.1.0 --user
```

Pluggable Workspace Tools:

```
jupyter:
  title: "Jupyter (Python, R, Julia)"
  iconUrl: "/assets/images/workspace-logos/Jupyter.svg"
  start: [ "/opt/domino/workspaces/jupyter/start" ]
  supportedFileExtensions: [ ".ipynb" ]
  httpProxy:
    port: 8888
    rewrite: false
    internalPath: "/{{ownerUsername}}/{{projectName}}/{{sessionPathComponent}}/{{runId}}/{{#if pathToOpen}}tree/{{pathToOpen}}{{/if}}"
    requireSubdomain: false
jupyterlab:
  title: "JupyterLab"
  iconUrl: "/assets/images/workspace-logos/jupyterlab.svg"
  start: [  "/opt/domino/workspaces/jupyterlab/start" ]
  httpProxy:
    internalPath: "/{{ownerUsername}}/{{projectName}}/{{sessionPathComponent}}/{{runId}}/{{#if pathToOpen}}tree/{{pathToOpen}}{{/if}}"
    port: 8888
    rewrite: false
    requireSubdomain: false
vscode:
  title: "vscode"
  iconUrl: "/assets/images/workspace-logos/vscode.svg"
  start: [ "/opt/domino/workspaces/vscode/start" ]
  httpProxy:
    port: 8888
    requireSubdomain: false
```

### Cluster Environment

Base image: `rayproject/ray-ml:2.6.3-py39`

Addditional Dockerfile instructions:

```
RUN pip install datasets==2.14.5 transformers==4.33.1 torch==2.0.1 accelerate==0.23.0 scikit-learn
USER root
RUN usermod -u 12574 ray
```

## Workspace Setup

Create a workspace with the following specs:

- `Workspace environment`: Use the workspace environment created above
- `Workspace hardware tier`: At least 6 cores, 16GB RAM. GPU is not required.
- `Ray cluster environment`: Use the cluster environment create above.
- `Ray worker hardware tier`: At least 1 GPU, 6 cores, 16GB RAM. 
- `Ray head hardware tier`: At least 6 cores, 16GB RAM. GPU is not required.

The other workspace settings can be configured to your own preference/requirements.

## Run the code

To run the sample, execute the following command in the terminal

```
python ray-tuning.py --data <MOUNTED PATH TO DATASET>
```

Once the execution has started, you can use the Domino Experiment UI to monitor the results.


