---
title: "Developer View"
icon: "fa-solid fa-cube"
description: "How to use blueprints with your repository"
type : "docs"
weight: 4
---

As a developer, you choose the appropriate [blueprint](https://docs.skylounge.io/concepts#blueprints) to automate workflows on your repository and then configure the automation with a [`skylounge.yml`](https://docs.skylounge.io/concepts#skyloungeyml) file that you add to the repository.

### Choosing the Blueprint

The first step is to choose the blueprint you want to use with your repository. You can browse the available blueprints at the [dashboard](https://dashboard.skylounge.io). They include [blueprints](https://docs.skylounge.io/concepts#blueprints) provided by your GitHub organization and others provided by SkyLounge itself. For each one, you can read documentation of the pre-defined [workflows](https://docs.skylounge.io/concepts#workflow-templates) it provides, as well as a skeleton `skylounge.yml` to configure the workflows for your repository.

Once you have chosen the appropriate blueprint, download the associated `skylounge.yml` file. You will work with that file in the next step.

### Configuring the Automation

The next step is to configure your chosen blueprint to work with your repository. You do this by adding and committing the appropriate `skylounge.yml` file at the root of your repository. The `skylounge.yml` for your chosen blueprint that you downloaded from the [dashboard](https://dashboard.skylounge.io) is pre-filled with the required URI to the blueprint and keys for all the data required to complete the configuration. All you need to do is fill in the values for these keys.

#### The `skylounge.yml`

The `skylounge.yml` file consists of at most three top-level keys.

```yaml
---
blueprint-uri: # REQUIRED URI to blueprint file in your GitHub organization.
workflows:     # OPTIONAL array of items configuring individual workflows.
  - ...
params:        # OPTIONAL key:value mapping available globally.
    ...
```

Only the `blueprint-uri` key is required. In theory, the other keys can be omitted if none of the workflows requires additional configuration or parameters. But in practice, all three keys will usually need to be filled in.

- `blueprint-uri:`

The `blueprint-uri:` key specifies the location of the chosen blueprint file in your GitHub organization. It is pre-filled in the skeleton `skylounge.yml` file downloaded from the [dashboard](https://dashboard.skylounge.io) and *should not be changed*.

- `workflows:`

The `workflows:` key collects individual configurations for the workflows specified in the chosen blueprint.

```yaml
workflows:
- workflow:    # REQUIRED name of individual workflow.
  jobs:        # OPTIONAL array of items configuring jobs.
  - ...
  params:      # OPTIONAL key:value mapping available within this workflow.
    ...
```

Each workflow item must specify the name of a single workflow with a `workflow:` key, may collect job configurations with a `jobs:` key, and may provide a `params:` key with parameters that are available to all jobs in this workflow.

Workflow items are only required for workflows that require configuration.

- `jobs:`

The `jobs:` key collects configurations for one more individual jobs with a workflow. Each job configuration item must specify the name of a `skylounge-job` with a `job:` key, may provide a `uri:` key with a URI to a file in GitHub that defines the `skylounge-job`, may provide a `steps:` key collecting configurations of one or more steps within the job, and may provide a `params:` key with parameters that are available within the `skylounge-job`.

As explained below, a `skylounge-job` is a composable / re-usable job defined using a SkyLounge extension of the [GitHub job specification](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_id). Workflows using a `skylounge-job` definition refer to it with the name given in the `job:` key.

```yaml
jobs:
- job:         # REQUIRED name of this `skylounge-job`.
  uri:         # OPTIONAL URI to `skylounge-job` file in GitHub.
  steps:       # OPTIONAL array of items configuring steps.
  - ...
  params:      # OPTIONAL key:value mapping available within this job.
    ...
```

- `steps:`

The `steps:` key collects configurations of one or more steps within a job. Each step configuration item must specify the name of a `skylounge-step` with a `step:` key and must provide a `uri:` key with a URI to a file in GitHub that defines one or more steps within the job.

As explained below, a `skylounge-step` is a composable / re-usable step defined using a SkyLounge extension of the [GitHub steps specification](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idsteps). Workflows using a `skylounge-step` definition refer to it with the name given in the `step:` key.

```yaml
steps:
- step:        # REQUIRED name of this `skylounge-step`.
  uri:         # URI to `skylounge-step` file in GitHub.
  params:      # OPTIONAL key:value mapping available within this step.
    ...
```

- `params:`

The `params:` key may be used at blueprint, workflow, or job levels of the `skylounge.yml` file. It is a *key:value* mapping that sets the (constant) value of parameters that used into the workflows and / or jobs and steps at the same level or below the `params:` within the `yaml` structure. We detail below how to *use* a parameter inside a workflow or a job or step definition, but *setting* a parameter is just matter of naming the parameter in the `<key>` and providing its <value>.

```yaml
params:
  <key>: <value>
  ...
```

#### SkyLounge's GitHub Actions Extensions

This section explains SkyLounge's extensions of GitHub actions to provide composable jobs, steps, and configuration parameters.

As explained above, jobs and steps can be defined by providing a URI to a file in GitHub. We refer to these files as `skylounge-job` and `skylounge-step` definitions. They can reside in your repository (for implementations you provide), your organization's `skylounge-library` repository (for reusable jobs/steps), or SkyLounge's [`public-skylounge-library`](https://github.com/sky-lounge/public-skylounge-library).

Definitions that reside in an application repository must be placed in a `skylounge` directory in the root of the repository, for SkyLounge to identify changes to the files. For example, you could have the following directories at the root:

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

- `skylounge-job`

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

- `skylounge-step`

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

- SkyLounge parameters

SkyLounge parameters extend the GitHub Actions syntax to enable configuration of composable `skylounge-job`s and `skylounge-step`s.

Parameters are used in workflow templates or `skylounge-job` definitions by surrounding a parameter name with double quote parenthesis: `((...))`. Parameter values are configured in the `skylounge.yml` file in your repository. For this reason, **do not use parameters to configure sensitive information**. Sensitive information should be stored using [GitHub Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets#using-encrypted-secrets-in-a-workflow) or another secure mechanism.

The `skylounge.yml` file supports configuring parameters at the `skylounge-job`, workflow, and blueprint (global) level. The most specific value takes precedence (job over workflow over blueprint). **[KPE: is this correct? Parameters cannot be set at the step level in the skylounge.yml file.]**

Parameters that start with an underscore (`_`) are optional and can be omitted. For example, `((_optional_parameter))` can be omitted.
