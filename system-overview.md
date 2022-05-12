# Skylounge Overview

Skylounge aims to be easily consumable by custom application teams using the familiar, industry standard GitHub capabilities. 

There are two major classes of inputs to Skylounge: the application specific configuration and the templates. The output is a set of [GitHub workflows](https://docs.github.com/en/actions/using-workflows/about-workflows) customized for each application and automatically kept up to date. 

Application specific configuration lives in a file called `skylounge.yml` at the root of the application repository. The templates are defined in one or more repositories and are defined below. 

## Lifecycle Templates

A lifecycle template is the top level construct in Skylounge. A lifecycle template defines a set of workflows that apply to a repository. For example, an organization may have a template to deploy a web application to Kubernetes and another template to deploy a web application to Google Cloud Run. The two lifecycle templates may leverage some of the same workflows or use a completely different set of workflows. 

Lifecycle templates are configured in a skylounge-definitions repository. An example configuration is provided below.

```
---

# Display name of the template used in the dashboard.
display-name: Web App deployed to Kubernetes

# Description of the workflow displayed to the user in the dashboard
description: A web application deployed to a Kubernetes instance

# Link to this file in GitHub
template-uri: sky-lounge/skylounge-definitions/lifecycle-templates/web-app-k8s/template-definition.yml


workflows:
- workflow: integration-test-k8s
  description: Deploys the app to Kubernetes in staging and runs integration tests
  template-uri: sky-lounge/skylounge-definitions/workflow-templates/web/integration-test-k8s/workflow.yml 

- workflow: web-security-scans
  description: Deploys the app to Cloud Foundry in staging and runs integration tests
  template-uri: sky-lounge/skylounge-definitions/workflow-templates/web/integration-test-cf/workflow.yml 

```

### Workflow Templates

A workflow is a construct of GitHub actions. Workflows provide automation tasks in response to events (like a code change/push).  Skylounge add the construct of a workflow template whereby users can configure and/or override certain parts of the automation through jobs and steps, defined below. All capabilities of GitHub workflows are supported in Skylounge, including the core units of automation jobs and steps.

Skylounge workflow templates adhere to the (GitHub workflow syntax)[https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions] while adding three capabilities: 

1. Overriding jobs: A workflow template can denote a (job)[https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow] to be provided by the application team. The intent is to allow the application team to implement the application specific portions of the workflow, like integration tests. A job can be marked for configuration by declaring the job name with the value of `skylounge-job`.

2. Overriding steps: A workflow template or job template can denote a (step)[https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idsteps] to be provided by the application team. A step can be marked for configuration by using the `skylounge-step` tag followed by a name. For example, a workflow may declare a build job that allows a development team to provide a custom build step, followed by an antivirus scan of the built artifact.

3. Injecting paramaters: Non-sensitive configuration (i.e. application names, number of instances, memory allocation, etc) can be configured in the skylounge.yml file and injected into the workflow at generation time. Configuration can be specified at the Job, Workflow, or Lifecycle level with the most specific value taking precdent (job over workflow over lifecycle). Sensitive information (i.e. credentials, certificates, IP addresses, etc) should always use (GitHub secrets)[https://docs.github.com/en/actions/security-guides/encrypted-secrets].

An example of a workflow template follows:

```
---
name: integration-test-k8s

on: 
  push:
    branches: 
    - develop
    paths: ((build_paths)) # A parameter configured in skylounge.yml

jobs:

  build:
    name: Build
    runs-on: ubuntu-latest
    concurrency: staging
    steps:
    - uses: actions/checkout@v3
    - skylounge-step: build-artifact # A skylounge step to be provided by the application team
    - uses: actions/clamav@v1
    - uses: actions/upload-artifact@v3
      with:
        name: ((artifact_name))
        path: ((artifact_path))

 integration-test: skylounge-job # A skylounge job to be provided by the application team

 ...
```


### GitHub workflow templates

GitHub actions define a concept of a [workflow template](https://github.blog/changelog/2020-06-23-github-actions-workflow-templates/). The GitHub version of a workflow template differs significantly from a Skylounge workflow template. For example, GitHub workflow templates do not allow users to define/override jobs and steps. They are also not automatically kept up to date if a workflow, job or step template changes. They are aimed at being a starting point which need to be edited and maintained by the development team.

## Job and Step Implementations

Job and step implementations can live in the application repository or be referenced from a shared skylounge-definitions repository. Both use the same GitHub syntax with additional support for parameters. The skylounge-definitions repository capability is outlined below.

## skylounge.yml

The skylounge.yml file provides configuration for each application registered with Skylounge. It allows application teams to specify the jobs, steps, and parameters outlined above. The skylounge.yml file lives in the main branch of the root repository of each application using Skylounge.

An example skylounge.yml follows:

```
# The lifecycle template to apply to this application
lifecycle-template: sky-lounge/skylounge-definitions/lifecycle-templates/web-app-k8s/lifecycle-template.yml 

workflows:
- workflow: integration-test-k8s
  jobs: 
  - job: build 
    steps:
    - step: build-artifact # Inject the build step in the build job
      template-uri: sky-lounge/skylounge-definitions/step-templates/gradlew/java17/build-steps.yml
    params:
      artifact_name:  skylounge-app.jar # Job level parameter
      artifact_path:  build/libs/skylounge-app.jar # Job level parameter
  - job: integration-test # Inject the integration test job from the application repository
    template-uri: my-company/my-app/ci/integration-test.yml
    ...

- workflow: web-security-scans
  params:
    scan_uri: https://mycompany.com/myapp # Workflow level parameter

params:
  build_paths: ["src/**","build.gradle","settings.gradle"] # Lifecycle template level parameter

```

## Template Definition Repositories

Skylounge provides the capability for software organizations to easily create and share custom templates. When an application developer registers a new repository with Skylounge, they are presented with lifecycle templates from the core [Skylounge set](https://github.com/sky-lounge/skylounge-definitions) as well as custom templates available in their GitHub organziation. 

A software organization can add their own templates to Skylounge by registering a repository named `skylounge-definitions` contained in their GitHub organization. Skylounge will ensure only authorized applications have access to these templates.

For example, assume you are an application developer looking to register your web application contained in a GitHub repository named `my-company/my-app` with Skylounge. When you add your web application's repository to Skylounge, you will be then taken to the dashboard to select the lifecycle template for your application. The list of available templates will include the [Skylounge managed core set](https://github.com/sky-lounge/skylounge-definitions) as well as any template available in the the `my-company/skylounge-definitions` repository if it exists.

The use of custom organization templates is optional however it provides customers the ability to easily define and codify the full lifecycle automation for applications in their organization. 