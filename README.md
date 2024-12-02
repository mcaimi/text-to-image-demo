# Stable Diffusion and Dreambooth on RedHat OpenshiftAI

Stable Diffusion demo running on JupyterHub and Red Hat Openshift AI. This repo houses the code of all Jupyter Notebooks and RHOAI Pipelines

- 1_experimentation.ipynb: Notebook with code that download tests and runs inference on a pretrained StableDiffusion model (uses diffusers and pytorch in the backend)
- 2_finetune.ipynb: Notebook that leverages DreamBooth to finetune the model, test the outcome and upload all new weights to an S3 bucket.
- 3_remote_inference.ipynb: Notebook that connects to the backend Inference Server on Openshift AI and tests inference from there.

## Requirements
The notebooks can be run as is on a JupyterHub instance on RHOAI.

This demo requires:

- A working S3 backend storage (see [here](https://github.com/mcaimi/minio-helm))
- Preferably a GPU with at least 16GB of VRAM (24 is way better)
- At least 2vCPU and 8GB of RAM on a kubernetes worker node.

## Setup
1. Create a data science project
2. Install MinIO from the mentioned Helm Chart and generate an AccessKey/SecretKey pair
3. Navigate to the Data Science Project and Instantiate a JupyterHub workbench.
4. Clone this repo in the Workbench, update the parameters.yaml file according to the requirements.
5. Create a pipeline server using the `Pipeline Artifacts` data connection.
6. Run the pipelines or individual notebooks.

## Demo Deployment

### Deploy the Inference Service

Deploy the Inference Server using the custom ServingRuntime based in KServe. Either via the RHOAI Console or using the provided Kustomization template:

```bash
# Provide the data connection name and the path inside the s3 bucket
$ cat data-connection-patch.yaml
apiVersion: serving.kserve.io/v1beta1
kind: InferenceService
metadata:
  name: sd-kserve
spec:
  predictor:
     storage:
        key: minio-data-connection-name  # Change this
        path: model_path_at_s3_endpoint  # Change this

# deploy
$ oc apply -k components/inference
```

### Deploy the frontend application

Just use the Openshift Console or the command line:

```bash
$ oc new-app --name sd-frontend --image=quay.io/marcocaimi/stable-diffusion-frontend:latest --env=INFER_URL="$(oc get isvc sd-kserve -ojsonpath='{.status.url}')"
$ oc expose service/sd-frontend
```

## Important

It is possible that the KServe component of RHOAI is disabled (i.e. single model serving option is grayed out).
In this case follow the documentation [here](https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed/2.10/html/serving_models/serving-large-models_serving-large-models#configuring-automated-installation-of-kserve_serving-large-models) to properly enable the feature.

