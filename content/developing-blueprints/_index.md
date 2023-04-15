---
title: "Developing Blueprints"
icon: "fa-solid fa-cubes"
description: "Build your automation and share it across your organization."
type : "docs"
weight: 5
---

If you can author GitHub workflows, you can author SkyLounge blueprints. Blueprints are stored in a structured GitHub repository we refer to as a 'library repository'. Library repositories are used to store blueprints, workflow templates, and job/step definitions.

### Library repositories

SkyLounge will treat any repository with a name ending in `skylounge-library` as a library repository (the repository can be named `skylounge-library`). Any user within your organization will have access to use the blueprints in your library repository(ies).

#### Repository structure

Library repositories are structured to improve performance. Your repository should contain the following root folders:

- blueprints
- jobs
- steps

##### blueprints

A library repository can contain zero or more blueprints. Each blueprint should be nested below the `blueprints` directory. It must be two directories below the `blueprints` directory. For example: `blueprints/gcp/cloud-run/`. This convention is in place for performance reasons.

* Blueprints are defined in a file named `blueprint.yml`. The json schema for `blueprint.yml` files is available here: https://github.com/sky-lounge/schemas/blob/main/blueprint.schema.json.

* A `skylounge.yml` file must be included in the same directory. This is a well-documented template that will be filled out by users of this blueprint. This is the exact file that will be downloadable through the dashboard. The json schema for the `skylounge.yml` files is available here: https://github.com/sky-lounge/schemas/blob/main/skylounge.schema.json.

* Workflow templates are included in a directory named `workflow-templates`.

Here is an example blueprint structure:

```
|- your-github-organization 
  |- skylounge-library 
    |- blueprints 
      |- gcp  
        |- cloud-run 
          |- blueprint.yml
          |- skylounge.yml
          |- workflow-templates
            |- development.yml
            |- production.yml
```

##### job definitions

Job definitions can be in any directory structure below the `jobs` directory. The schema of a job definition is exactly the same as a [GitHub job](https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow) nested under the `jobs` tag in workflow, except in a standalone file. A job definition file contains a single job.

For example, a job definition to deploy a container image to Google Cloud Run be defined in a file named `deploy.yml` located at `your-github-org/skylounge-library/jobs/gcp/cloud-run/deploy/deploy.yml`. 

```
deploy:
  needs: build
  name: Deploy to Cloud Run
  runs-on: ubuntu-latest
  env:
    REGISTRY: ((registry_url))
  steps:
    - name: Checkout branch
      uses: actions/checkout@v3
    - name: Set image and service name
      run: |
        sed -i -e 's%${IMAGE_NAME}%'"$IMAGE_NAME"'%g' ./service.yaml
        sed -i -e 's%${GCP_PROJECT}%'"$GCP_PROJECT"'%g' ./service.yaml
      env:
        IMAGE_NAME: "${{ env.REGISTRY }}/${{ github.event.repository.name }}:${{ github.sha }}"
        GCP_PROJECT: ((gcp_project))
...
```

##### step definitions

Step definitions can be in any directory structure below the `steps` directory. The schema of a step definition is exactly the same as the [`steps` block](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idsteps) in a GitHub job, except in a standalone file. A step definition may contain more than one step.

For example, a step definition which uses [pack](https://buildpacks.io/docs/tools/pack/) to build a container image for an app using gradle wrapper could be defined in a file named `build-container-image.yml` located at `your-github-org/skylounge-library/steps/pack/gradlew/build-container-image.yml`. 

```
---
steps:
  - name: Setup Pack
    uses: buildpacks/github-actions/setup-pack@v4.8.0
  - name: Pack build
    run: |
      set -x
      pack build --volume $HOME/.gradle:/home/cnb/.gradle:rw \
        ${{ env.IMAGE_NAME }} \
        --builder paketobuildpacks/builder:base \
        --env BP_JVM_VERSION=${{ env.BP_JVM_VERSION}} \
        --path ${{ env.JAR_PATH }}
    env:
      BP_JVM_VERSION: ((jvm_version))
      JAR_PATH: ((jar_path))
```


### Registering your definitions repository

Register your library repository through the GitHub interface the same way you would register an application. To do this:

* Go to your organization in GitHub and click "Settings"
* Under "Integrations" on the left-hand side, click "GitHub Apps"
* Next to SkyLoungeio, click "Configure"
* Under "Repository access", click "Select repositories"
* Search for and select your library repository
* Click "Save"

Your blueprints will now show up in the dashboard.

