

## Jobs

Jobs can be replaced in a workflow. To declare a job for replacement in a workflow, specify the key as name of the job, and `skylounge-job` as the value.

For example, the `build` job in the below workflow template will be replaced:

```
...
jobs:
  build: skylounge-job
```

Include the `build` job declaration in the skylounge.yml.


### Why not `skylounge-job: build`?

In the GitHub workflow spec, the `jobs` field is a hash or dictionary, not an array. Therefore, you cannot have multiple entries with a key of `skylounge-job`. Using this syntax would ensure that only a single job could be replaced.

### Job Templates

Job templates should start with the name of the job. While the name in the workflow template will be used in the workflow, the job template still needs a name. For example:

```
---
gradle_build: # The job name
  name: Gradle Build
  runs-on: ubuntu-latest
  concurrency: staging
  steps:
  - uses: actions/checkout@v2
  - uses: actions/setup-java@v2
    with:
      distribution: microsoft
      java-version: 17
  - name: Setup Gradle
    uses: gradle/gradle-build-action@v2
  - name: Gradle test
    run: ./gradlew test
  - name: Gradle assemble
    run: ./gradlew assemble
  - uses: actions/upload-artifact@v3
    with:
      name: staging.jar
      path: build/libs/*.jar
```

## Steps

The steps field is an array of objects. The objects may be `uses` or `name`. Therefore, we declare steps as `skylounge-step: step_name`. Be sure to include the `step_name` declaration in the skylounge.yml.

For example, the template specified by `build-artifact` will be inserted into the workflow template below:

```
...
jobs:
  build:
    ...
    steps:
    - uses: actions/checkout@v3
    - skylounge-step: build-artifact
    - uses: actions/upload-artifact@v3
      with:
        name: ((artifact_name))
        path: ((artifact_path))
```

### Step Templates

A step template must start with the `steps:` tag and can include one or more steps. For example:

```
steps:
---
steps:
- uses: actions/setup-java
  with:
    distribution: microsoft
    java-version: 17
- name: Setup Gradle
  uses: gradle/gradle-build-action
- name: Gradle test
  run: ./gradlew test
- name: Gradle assemble
  run: ./gradlew assemble
```

All of the steps above will be inserted into the workflow.

## Parameters

Parameters are declared in templates using double quote notation: `((a-parameter))`.

Application specific parameters are configured in the skylounge.yml file. Parameters can be included at the Job, Workflow, or Lifecycle levels. The most specific parameter will be used when completing the workflow file - job, then workflow, then lifecycle. 

Therefore, if you wanted a global parameter of "application_name", you would declare this a the lifecycle level in the skylounge.yml. If you needed to override this in a specific job, you could add that override a the job level.

Parameters cannot be set at the step level.


