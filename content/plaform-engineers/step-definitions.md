---
title: "Step definitions"
layout: "docs"
weight: 5
---

A step definition file is a `yaml` file following the [GitHub steps specification](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idsteps), with two extensions. First, it must have exactly one top-level key named `steps:`. Additionally, the file may use SkyLounge parameters. A step definition may contain one or more steps.

The example below shows an excerpt from a `gradlew` build step, with a SkyLounge parameter specifying the `java-version`.

```yaml
---
Path: <your-organization>/skylounge-library/steps/gradlew/build/step.yml 

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
```

Steps should be stored in your `skylounge-library` in the steps directory. 

#### Parameter documentation

Additionally, you need to provide a file named `params.yml` in the same directory as the `step.yml`. This file is used when generating a `skylounge.yml` based on a blueprint profile. The file documents the parameters in the job definition for developer consumption and will be inserted into generated `skylounge.yml`. The file starts with a single key `params`.

Below is an example `params.yml` for our sample step above:

```yaml
params:
  jvm_version: # The JVM version to use
```