---
title: "Job definitions"
layout: "docs"
weight: 4
---

A job definition is a `yaml` file following the [GitHub job specification](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_id), with two key differences. First, it must have exactly one top-level key: the name of the job defined as per the skylounge.yml. Additionally, the file may use SkyLounge parameters (defined below). A job definition may only define a single job.

The example below shows an excerpt from a job to deploy to Google Cloud Run.

```yaml
Path: <your-organization>/skylounge-library/jobs/gcp/cloud-run/deploy/job.yml

---
deploy:
  needs: build
  name: Deploy to development
  runs-on: ubuntu-latest
  env:
    REGISTRY: ((registry_url))
  steps:
    - name: Checkout branch
      uses: actions/checkout@v3
    - id: auth
      name: Authenticate to Google Cloud
      uses: google-github-actions/auth@v0
      with:
        credentials_json: "${{ secrets.GOOGLE_CREDENTIALS_DEV }}"
    - id: deploy
      uses: google-github-actions/deploy-cloudrun@v0
      with:
        metadata: ./service.yaml
      params: 
        gcp_project: ((gcp_project))
```

Jobs should be stored in your `skylounge-library` in the jobs directory. 

#### Parameter documentation

Additionally, you need to provide a file named `params.yml` in the same directory as the `job.yml`. This file is used when generating a `skylounge.yml` based on a blueprint profile. The file documents the parameters in the job definition for developer consumption and will be inserted into generated `skylounge.yml`. The file starts with a single key `params`.

Below is an example `params.yml` for our sample job above:

```yaml
params:
  registry_url: # The URL of the Google artifact registry where the image is stored.
  gcp_project: # The name of the GCP project to deploy to.
```

