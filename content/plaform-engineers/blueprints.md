---
title: "Blueprints"
layout: "docs"
weight: 1
---

If you can author GitHub workflows, you can author SkyLounge blueprints. Blueprints are stored a`library repository`. Library repositories store blueprints, workflow templates, and job/step definitions.

### Repository structure

Library repositories are structured to improve performance. Therefore, your repository should contain the following root folders:

```
|- blueprints
|- jobs
|- steps
```

Here is an example blueprint structure:

``` 
|- blueprints 
  |- gcp  
    |- cloud-run # The GCP Cloud Run blueprint starts here
      |- blueprint.yml
      |- skylounge.yml
      |- workflow-templates
        |- development.yml
        |- production.yml
```

### Registering your definitions repository

Register your library repository through the GitHub interface like a managed repository. To do this:

* Go to your organization in GitHub and click "Settings"
* Under "Integrations" on the left-hand side, click "GitHub Apps"
* Next to SkyLoungeio, click "Configure"
* Under "Repository access", click "Select repositories"
* Search for and select your library repository
* Click "Save"

Your blueprints will now be available and show up in the [dashboard](https://dashboard.skylounge.io).



### blueprint.yml

A library repository can contain zero or more blueprints. Each blueprint should be nested below the `blueprints` directory. It must be two directories below the `blueprints` directory. For example: `/blueprints/apps/web/`. This convention is in place for performance reasons.

Blueprints are defined in a file named `blueprint.yml`. The json schema for `blueprint.yml` files is available here: https://github.com/sky-lounge/schemas/blob/main/blueprint.schema.json.

Below is a sample blueprint.yml with field descriptions:

```yaml
display-name: Blueprint Name # Name displayed in the dashboard
description: |
  The description shows in dashboard on the blueprints tab.
documentation: |
  This field is for detailed documentation, viewable via the `blueprint details` link in the dashboard.
tags: # A list of tags used as visual cues in the dashboard.
  - container-build
  - virus-scan
  - gcp
  - penetration-test
weight: 1 # Used to set the display order of blueprints
workflow-templates: # Lists the workflow templates included in this blueprint
  - name: workflow-name
    description: |
      The description shows in the dashboard.
    uri: sky-lounge/skylounge-library/blueprints/apps/web/workflow-templates/development.yml # The uri to the workflow template. For performance reasons, this should be in the `workflow-templates` directory of the blueprint.
    documentation: |
      Detailed documentation viewable in the dashboard via the `workflow details` link.
    branch: main # The branch this workflow operates on. This should usually be main.
    icon: bi bi-code-slash # An icon to display in the dashboard for the workflow.
```

### skylounge.yml template

A `skylounge.yml` template file must be included in the same directory as the `blueprint.yml`. This is a well-documented template that will be filled out by users of this blueprint and used during the generation when using blueprint profiles. This is the exact file that will be downloadable through the dashboard. The json schema for the `skylounge.yml` files is available here: https://github.com/sky-lounge/schemas/blob/main/skylounge.schema.json.

An example `skylounge.yml` follows:

```yaml
---
blueprint-uri: sky-lounge/skylounge-library/blueprints/app/web/blueprint.yml
workflows:
  - workflow: development
    jobs:
      - job: build
        steps:
          - step: unit-test
            uri: # Enter the URI to the step definition used to run unit tests for your application. Example: sky-lounge/skylounge-library/steps/unit-test/java/gradlew/unit-test.yml
          - step: build-container-image
            uri: # Enter the URI to the step definition used to build the container image for your application. Example: sky-lounge/skylounge-library/steps/build/pack/java/gradlew/build.yml
          - step: 
        params:
          build_paths: # An array of paths which trigger the build job on change. Example: ["src/**", "build.gradle", "service.yaml"]
      - job: deploy
      - job: integration-test
        uri: # The URI to your integration tests. Example: sky-lounge/hello-app/skylounge/integration-test.yml
    params:
      gcp_project: # The Google Cloud project name where the application is deployed. Example: skylounge-dev
      cloud_run_domain: # The domain to configure for your application. Example: dashboard.skylounge.dev
  - workflow: production
    jobs:
      - job: deploy
    params:
      gcp_project: # The Google Cloud project name where the application is deployed. Example: skylounge-prod
  - workflow: daily-scans
    jobs:
      - job: owasp-zap-full-scan
        params:
          endpoint_to_scan: # The production endpoint to scan. Example: https://dashboard.skylounge.io
params:
  registry_url: # The artifact registry url where container images are stored. Example: us-central1-docker.pkg.dev/skylounge-common/skylounge-registry
```


