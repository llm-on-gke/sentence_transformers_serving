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
export REGION=us-east1
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
  --cluster-version=1.29
```

Validations:
```
gcloud container clusters get-credentials ${CLUSTER_NAME} --location=${REGION}
```
Make sure cluster in ready mode

## Deploy the embedding model

Run the following command to deploy embedding model to GKE cluster created
Update the image path in embeddings.yaml file, then Run the following command to deploy embeddings model
```
kubectl apply embeddings.yaml

```
Validations: 
kubectl get po
NAME                               READY   STATUS    RESTARTS   AGE
embedding-serve-6fdc664fbb-8r2r6   1/1     Running   0          10m
│17-May 19:35 admin_ in ⎔ rick-vertex-ai ⪧ ~/sentence_transformers_serving ⎈N/A Ⴤ [master] ക 

└─⪧ kubectl get svc
NAME              TYPE           CLUSTER-IP      EXTERNAL-IP    PORT(S)          AGE
embedding-serve   LoadBalancer   34.118.237.68   35.227.77.40   8080:30518/TCP   12m
kubernetes        ClusterIP      34.118.224.1    <none>         443/TCP          20m

## curl tess with Embedding model
```
curl SVC_IP:8080/embed -X POST \
-d '{"inputs":"What is Deep Learning?"}' \
-H 'Content-Type: application/json'
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