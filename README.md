## Sentence Transformers serving

see original [paper](https://arxiv.org/abs/1908.10084) 
and [source](https://github.com/UKPLab/sentence-transformers#application-examples)

# Getting Started

## List of Models for Best Sentence Embeddings (taken from [source](https://github.com/UKPLab/sentence-transformers/blob/master/README.md))

## Prepare the environment

Use Cloud Shell to manage resources hosted on Google Cloud. Cloud Shell comes preinstalled with the software you'll need for this tutorial, including kubectl and gcloud CLI.

To set up your environment with Cloud Shell, follow these steps:

In the Google Cloud console, launch a Cloud Shell session by clicking Cloud Shell activation icon Activate Cloud Shell in the Google Cloud console. This launches a session in the bottom pane of Google Cloud console.

Set the default environment variables:

gcloud config set project PROJECT_ID
export PROJECT_ID=$(gcloud config get project)
export REGION=REGION
export CLUSTER_NAME=embedding

Enable the required APIs to create a GK cluster:
```
gcloud services enable compute.googleapis.com container.googleapis.com
```


## Build Sentence Transformer embedding container image
Go go cloud shell, Clone the repo:
```
git clone https://github.com/llm-on-gke/sentence_transformer_serving
cd sentence_transformer_serving
```
update the cloudbuild.yaml and update the target container image repo and path
then kick of cloud build:
```
gcloud builds submit . 
```

## Create GKE Cluster
Run the following to create GKE Autopilot cluster:
```
gcloud container clusters create-auto ${CLUSTER_NAME} \
  --project=${PROJECT_ID} \
  --region=${REGION} \
  --release-channel=rapid \
  --cluster-version=1.28
```

Validations:
```
gcloud container clusters get-credentials ${CLUSTER_NAME} --location=${REGION}
```
Make sure cluster in ready mode

## Deploy the embedding model

Run the following command to deploy embedding model to GKE cluster created
```
kubectl apply embeddings.yaml

```

## POST request from python
```python
import json
import requests

url = f"http://0.0.0.0:8080/"
data = {"inputs": "A man is eating food.", "A man is eating a piece of bread."}
result = requests.post(url, json=data)

embeddings = json.loads(result.content)
```

## requirements
```text
sentence_transformers>=2.7.0
Flask
requests
waitress
```

# References

https://github.com/UKPLab/sentence-transformers

https://arxiv.org/abs/1908.10084