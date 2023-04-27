---
title: "Authoring jobs and steps"
layout : "docs"
weight: 1
---

Repository-specific job and step definitions must reside in your managed repository in a `skylounge` directory in the repository's root so that SkyLounge can identify file changes. For example:

```
...
skylounge\
    jobs\
       integration-test.yml
       ...
    steps\
       custom-container.yml
       ...
...
```

#### Custom job definitions

A job definition is a `yaml` file following the [GitHub job specification](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_id), with two key differences. First, it must have exactly one top-level key: the name of the job defined as per the skylounge.yml. Additionally, the file may use SkyLounge parameters (defined below). A job definition may only define a single job.

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

#### Custom step definitions

A step definition file is a `yaml` file following the [GitHub steps specification](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idsteps), with two extensions. First, it must have exactly one top-level key, the `steps:` key. Additionally, the file may use SkyLounge parameters (defined below). A step definition may contain one or more steps.

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

#### SkyLounge parameters in job and step definitions

SkyLounge parameters extend the GitHub Actions syntax to enable configuration through a skylounge.yml file. Parameters are defined in custom job and step definitions using double parentheses notation: `((parameter))`. Parameter names that start with an underscore (`_`) are optional and can be omitted. For example, `((_optional_parameter))` can be omitted.
