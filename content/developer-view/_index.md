---
title: "Developer View"
icon: "fa-solid fa-cube"
description: "How to use blueprints with your repository"
type : "docs"
weight: 4
---

As a developer, you choose the appropriate [blueprint](https://docs.skylounge.io/concepts#blueprints) to automate workflows on your repository and then configure the automation with a [`skylounge.yml`](https://docs.skylounge.io/concepts#skyloungeyml) file that you add to the repository.

### Choosing the Blueprint

First choose the blueprint you want to use with your repository. You can browse the available blueprints at the [dashboard](https://dashboard.skylounge.io). They include [blueprints](https://docs.skylounge.io/concepts#blueprints) provided by your GitHub organization and others provided by SkyLounge itself. For each one, you can read documentation of the pre-defined [workflows](https://docs.skylounge.io/concepts#workflow-templates) it provides, as well as a skeleton `skylounge.yml` to configure the workflows for your repository.

Once you have chosen the appropriate blueprint, download the associated `skylounge.yml` file and save at the root of your repository. You will work with that file in the next step.

### Configuring the Automation

Next configure your chosen blueprint to work with your repository. You do this by editing and committing the appropriate `skylounge.yml` file at the root of your repository. The `skylounge.yml` that you downloaded from the [dashboard](https://dashboard.skylounge.io) is pre-filled with the required URI to the blueprint and certain empty (but documented!) keys for the data required to complete the configuration.

The missing configuration data can be any combination of:

- job `uri:`s to `skylounge-job` files defining workflow jobs,
- step `uri:`s to a `skylounge-step` files defining job steps, or
- `params.<key>s` item for parameter named \<key\>.

The following shows all three kinds in an imaginary `skylounge.yml` file:

```yaml
blueprint-uri: example-org/public-skylounge-library/blueprints/example/blueprint.yml
workflows:
  - workflow: dev-example
    jobs:
      - job: setup
        uri:
          # Job URI for `setup`.
          # Example: example-org/our-app/skylounge/jobs/setup.yml
        params:
          setup-param: # Value for parameter `setup-param` in step `setup`.
      - job: main
        steps:
          - step: custom-step
            uri:
              # Step URI for `custom-step`.
              # Example: example-org/our-app/skylounge/steps/custom-step.yml
        params:
          main-param: # Value for parameter `main-param` in job `main`.
params:
  global-param: # Value for global parameter `global-param`.
```

### Defining SkyLounge Jobs and Steps

Jobs and steps can be defined by providing a URI to a file in GitHub. We refer to these files as `skylounge-job` and `skylounge-step` definitions. Your organization can provide them your organization's `skylounge-library` repository (as reusable jobs and steps), in which case they would reside in your organization's "`skylounge-library`" repository. You can provide them yourself, in which case you must put them in your repository (for implementations you provide). Or you might find suitable ones in SkyLounge's [`public-skylounge-library`](https://github.com/sky-lounge/public-skylounge-library).

#### `skylounge` directory

Definitions that reside in your repository must be placed in a "`skylounge`" directory in the root of the repository, so that SkyLounge can identify changes to the files. For example, you could have the following directories at the root:

```
...
skylounge\
    jobs\
       integration-test.yml
       ...
    steps\
       build-container.yml
       ...
...
```

#### `skylounge-job` definition

A `skylounge-job` definition is a `yaml` file following the [GitHub job specification](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_id), with one extension.

As a `yaml` file, it must begin with three hyphens ("`---`"). Then it must have exactly one top-level key, which is the name of the `skylounge-job` being defined. (This corresponds to the `<job_id>` in the GitHub job specification.) The rest of the file follows the GitHub job specification, with the extension that it may also use SkyLounge parameters.

A workflow using a `skylounge-job` definition refers to it by the name of the `skylounge-job` given at the top level of the `yaml` structure. The job will be inserted into the workflow with any SkyLounge parameters interpolated into it using values set in the `skylounge.yml` file for this `skylounge-job`, the workflow or blueprint containing it.

The example below shows an excerpt from an `integration-test` job, without any SkyLounge parameters.

```yaml
---
integration-test: # The name of the `skylounge-job`
  needs: load-balancer
  name: Integration Test
  runs-on: ubuntu-latest
  steps:
  - name: Checkout develop branch
    uses: actions/checkout@v3
...
```

#### `skylounge-step` definition

A `skylounge-step` definition file is a `yaml` file following the [GitHub steps specification](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idsteps), with one extension.

As a `yaml` file, it must begin with three hyphens ("`---`"). Then it must have exactly one top-level key, which is "`steps:`" (This corresponds to the `steps` key in the GitHub steps specification.) The rest of the file follows the GitHub steps specification, with the extension that it may also use SkyLounge parameters. The definition can contain multiple steps.

A workflow uses a `skylounge-step` definition when a `skylounge-job` refers to it using the `step:` name and `uri:` within a `skylounge-job` used in a workflow. All steps will be inserted into the `skylounge-job` within the workflow, with any SkyLounge parameters interpolated into it using values set in the `skylounge.yml` file for the job, workflow, or blueprint containing it.

The example below shows an excerpt from a `gradlew` build step, with a SkyLounge parameter specifying the `java-version`.

```yaml
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

#### SkyLounge Parameters

SkyLounge parameters extend the GitHub Actions syntax to enable configuration of composable `skylounge-job`s and `skylounge-step`s.

Parameters are used in workflow templates or `skylounge-job` definitions by surrounding a parameter name with double quote parenthesis: `((...))`. Parameter values are configured in the `skylounge.yml` file in your repository. For this reason, **do not use parameters to configure sensitive information**. Sensitive information should be stored using [GitHub Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets#using-encrypted-secrets-in-a-workflow) or another secure mechanism.

The `skylounge.yml` file supports configuring parameters at the `skylounge-job`, workflow, and blueprint (global) level. The most specific value takes precedence (job over workflow over blueprint). Parameters cannot be set at the step level in the skylounge.yml file.

Parameters that start with an underscore (`_`) are optional and can be omitted. For example, `((_optional_parameter))` can be omitted.
