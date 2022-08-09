---
title: "Using Blueprints"
icon: "fa-solid fa-cube"
description: "How to use blueprints with your repository"
type : "docs"
weight: 4
---

The first step is choosing the blueprint you want to use. You can browse the available blueprints from the [dashboard](https://dashboard.skylounge.io) along with the blueprint and workflow documentation. The list of available blueprints includes blueprints in your GitHub organization and SkyLounge-provided blueprints.

### Configuring Jobs and Steps
            
Jobs and steps are configured by providing a URI to a file in GitHub. We refer to these files as job or step definitions. The job or step definition can reside in your repository (for implementations you provide), your organization's `skylounge-definitions` repository (for reusable jobs/steps), or the SkyLounge provided `skylounge-definitions` repository.

#### Implementing Jobs

Composable job definitions follow the [GitHub job specification](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_id). In addition, you need to start the job file with the name of the job. The example below shows an excerpt from an `integration-test` job implementation. The remainder of the job definition follows the GitHub job specification. Additionally, you can use SkyLounge parameters in job files.

```
---
integration-test: # The name of the job
  needs: load-balancer
  name: Integration Test
  runs-on: ubuntu-latest
  steps:
  - name: Checkout develop branch
    uses: actions/checkout@v3
...
```

#### Implementing Steps
            
Composable step definitions follow the [GitHub steps specification](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idsteps) starting with a `steps` declaration. A step definition can contain multiple steps. All steps will be inserted into the workflow. The example below shows an excerpt from a gradlew build step. Additionally, you can use SkyLounge parameters in step files.

```
---
steps:
  - name: Set up Java
    uses: actions/setup-java@v3
    with:
      distribution: "liberica"
      java-version: ((jvm_version))
      cache: "gradle"
  - name: Unit test
    run: ./gradlew test
  - name: Build Jar
    run: ./gradlew assemble
...
```
                        
### Configuring Parameters
            
Parameters are declared in workflow templates, job or step definitions using double quote notation: `((a-parameter))`. Parameter values are configured in the `skylounge.yml` file in your repository. For this reason, **do not use parameters to configure sensitive information**. Sensitive information should be stored using [GitHub Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets#using-encrypted-secrets-in-a-workflow) or another secure mechanism.
            
The `skylounge.yml` file supports configuring parameters at the job, workflow, or blueprint level. The most specific value takes precedence (job over workflow over blueprint). Parameters cannot be set at the step level in the skylounge.yml file.

#### Optional Parameters
            
Parameters that start with an underscore (`_`) are optional and can be omitted. For example, `((_optional_parameter))` can be omitted.