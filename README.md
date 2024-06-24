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
