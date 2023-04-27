---
title: "Blueprint profiles"
layout: "docs"
weight: 6
---

A blueprint profile directs SkyLounge on how to apply blueprints, job and step definitions, and parameters based on the [labels](https://docs.github.com/en/issues/using-labels-and-milestones-to-track-work/managing-labels) on a repository. This further simplifies the onboarding of managed repositories by developer teams. Blueprint profiles are configured and maintained by platform teams. 

### Managing blueprint profiles

Blueprint profiles must be stored in a repository named `skylounge-library`. Blueprint profiles are configured in `/blueprints/profiles.yml`. The library repository must be registered with SkyLounge. The `profiles.yml` specifies all blueprint profiles for the organization. 

Labels can be used to select blueprints as well as job and step definitions. Additionally, blueprint profiles can be used to provide configuration values for parameters at the blueprint, workflow, job, or step level. Labels used in blueprint profiles are arbitrary strings and can be any value.

Blueprint profiles are used to generate a `skylounge.yml` file for a managed repository according to the labels on that repository. They have the added benefit of merging job and step definition parameters into the generated `skylounge.yml`. The example below will demonstrate these features.

### Example

The example below demonstrates creating and applying a blueprint profile for web applications. The yml files below have comments to explain what is happening. 

In this example, the organization:

* Has applications written in JVM languages that use Gradle wrapper and applications written in nginx
* Allows deployment to Google Cloud Run or the Cloud.gov platform

```yaml
Path: <your-organization>/skylounge-library/blueprints/profiles.yml

---
blueprints:
  - label: webapp # The blueprint below will be applied when the repository has the `webapp` label.
    uri: sky-lounge/skylounge-library/blueprints/fisma/web-app/blueprint.yml # This is the blueprint uri
    params:
      registry_url: us-central1-docker.pkg.dev/skylounge-common/skylounge-registry # This parameter will be set at the blueprint level in generated skylounge.yml files. 
    workflows:
      development:
        jobs:
          build:
            - steps:
                unit-test:
                  - label: gradlew # This step definition will be used for the unit-test step in the build job of the development workflow when the repository has the `gradlew` label.
                    uri: sky-lounge/skylounge-library/steps/unit-test/gradlew/unit-test.yml
                    params:
                      jvm_version: 17 # This parameter will be included in the skylounge.yml for the step.
                  - label: nginx # This step definition will be used for the unit-test step in the build job of the development workflow when the repository has the `nginx` label.
                    uri: sky-lounge/skylounge-library/steps/unit-test/nginx/unit-test.yml
                build-container-image:
                  - label: gradlew # This step definition will be used for the build-container-image step in the build job of the development workflow when the repository has the `gradlew` label.
                    uri: sky-lounge/skylounge-library/steps/pack/gradlew/build.yml
                    params:
                      jvm_version: 17 # This parameter will be included in the skylounge.yml for the step.
                  - label: nginx # This step definition will be used for the build-container-image step in the build job of the development workflow when the repository has the `nginx` label.
                    uri: sky-lounge/skylounge-library/steps/pack/nginx/build.yml
          deploy:
            - label: cloud-run # This job will be used for deployment in the development workflow when the repository has the `cloud-run` label.
              uri: sky-lounge/skylounge-library/jobs/gcp/cloud-run/deploy/deploy.yml
              params: # These parameters will be included in the skylounge.yml for the deploy job.
                tf_state_bucket: tfstate.skylounge.dev
                dns_zone_name: skyloungedev
                cloud_run_location: us-central1
            - label: cloud-gov # This job will be used for deployment in the development workflow when the repository has the `cloud-gov` label.
              uri: sky-lounge/skylounge-library/jobs/cloudfoundry/deploy/deploy.yml
        params: # This parameter will be included in the skylounge.yml for the development workflow.
          project: skyloungedev
      production:
        jobs:
          deploy:
            - label: cloud-run # This job will be used for deployment in the production workflow when the repository has the `cloud-run` label.
              uri: sky-lounge/skylounge-library/jobs/gcp/cloud-run/deploy/deploy.yml
              params:
                tf_state_bucket: tfstate.skylounge.io
                dns_zone_name: skyloungeio
                cloud_run_location: us-central1
            - label: cloud-gov # This job will be used for deployment in the production workflow when the repository has the `cloud-gov` label.
              uri: sky-lounge/skylounge-library/jobs/cloudfoundry/deploy/deploy.yml
        params: # This parameter will be included in the skylounge.yml for the development workflow.
          project: skyloungeio
```

Given the following skylounge.yml template for the blueprint:

```yaml
Path: sky-lounge/skylounge-library/blueprints/fisma/web-app/skylounge.yml

---
blueprint-uri: sky-lounge/skylounge-library/blueprints/fisma/web-app/blueprint.yml
workflows:
  - workflow: development
    jobs:
      - job: build
        steps:
          - step: unit-test
            uri: # Enter the URI to the step definition used to run unit tests for your application. Example: sky-lounge/public-skylounge-library/steps/unit-test/java/gradlew/unit-test.yml
          - step: build-container-image
            uri: # Enter the URI to the step definition used to build the container image for your application. Example: sky-lounge/public-skylounge-library/steps/build/pack/java/gradlew/build.yml
        params:
          build_paths: # An array of paths which trigger the build job on change. Example: [\"src/**\", \"build.gradle\", \"service.yaml\"]
      - job: deploy
      - job: integration-test
        uri: # The URI to your integration tests. Example: sky-lounge/hello-app/skylounge/integration-test.yml
  - workflow: production
    jobs:
      - job: deploy
  - workflow: daily-scans
    jobs:
      - job: owasp-zap-full-scan
        params:
          endpoint_to_scan: # The production endpoint to scan. Example: https://dashboard.skylounge.io
params:
  registry_url: # The artifact registry url where container images are stored. Example: us-central1-docker.pkg.dev/skylounge-common/skylounge-registry
```

A managed repository with the labels of `webapp`, `gradlew`, and `cloud-run` will receive a pull request with the following generated `skylounge.yml`. The additional parameters below are inserted by SkyLounge from the job and step definitions (for example the `jar-path` under development > build > build-container-image and `build_paths` under development > build). In doing so, SkyLounge has generated the complete `skylounge.yml` based on the job and step definition selections. Again, this is easier for developer teams.

```yaml
blueprint-uri: sky-lounge/skylounge-library/blueprints/fisma/web-app/blueprint.yml
workflows:
- workflow: development
  jobs:
    - job: build
      steps:
        - step: unit-test
          uri: sky-lounge/skylounge-library/steps/unit-test/gradlew/unit-test.yml
          params:
            jvm_version: 17
        - step: build-container-image
          uri: sky-lounge/skylounge-library/steps/pack/gradlew/build.yml
          params:
            jar_path: # Path to the jar file
            jvm_version: 17
      params:
        build_paths: # An array of paths which trigger the build job on change. Example: [\"src/**\", \"build.gradle\", \"service.yaml\"]
    - job: deploy
      uri: sky-lounge/skylounge-library/jobs/gcp/cloud-run/deploy/deploy.yml
      params:
        cloud_run_domain: # The domain to configure for your application. Example: dashboard.skylounge.dev
        cloud_run_invoker: # OPTIONAL - The name of the user allowed to invoke your Cloud Run service. For any authenticated Google user, use `allUsers`. For public access, leave it empty. Example: allUsers
        cloud_run_location: us-central1
        dns_zone_name: skyloungedev
        tf_state_bucket: tfstate.skylounge.dev
    - job: integration-test
      uri: # The URI to your integration tests. Example: sky-lounge/hello-app/skylounge/integration-test.yml
  params:
    project: skyloungedev
- workflow: production
  jobs:
    - job: deploy
      uri: sky-lounge/skylounge-library/jobs/gcp/cloud-run/deploy/deploy.yml
      params:
        cloud_run_domain: # The domain to configure for your application. Example: dashboard.skylounge.dev
        cloud_run_invoker: # OPTIONAL - The name of the user allowed to invoke your Cloud Run service. For any authenticated Google user, use `allUsers`. For public access, leave it empty. Example: allUsers
        cloud_run_location: us-central1
        dns_zone_name: skyloungeio
        tf_state_bucket: tfstate.skylounge.io
  params:
    project: skyloungeio
- workflow: daily-scans
  jobs:
    - job: owasp-zap-full-scan
      params:
        endpoint_to_scan: # The production endpoint to scan. Example: https://dashboard.skylounge.io
params:
  registry_url: us-central1-docker.pkg.dev/skylounge-common/skylounge-registry
```

The development team can fill in the additional details for the managed repository before committing the file.

