---
title: "Workflow templates"
layout: "docs"
weight: 3
---

Workflow templates are [GitHub workflows](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions) with the addition of `skylounge-jobs`, `skylounge-steps`, and parameters. Anything you can automate in a GitHub workflow, you can automate in a SkyLounge workflow template.

When authoring workflow templates, we first recommend developing a GitHub workflow in a repository. You can then dissect this workflow into a template by identifying:

* Jobs that should be configurable via job definitions
* Steps that should be configurable via step definitions
* Configuration to be parameterized 

### SkyLounge jobs

To declare a job for configuration, specify the key as name of the job, and `skylounge-job` as the value.

For example, the `build` job in the below workflow template will be replaced:

```
...
jobs:
  build: skylounge-job
```

Include the `build` job declaration in the skylounge.yml.

**Why not `skylounge-job: build`?**

In the GitHub workflow spec, the `jobs` field is a hash or dictionary, not an array. Therefore, you cannot have multiple entries with a key of `skylounge-job`. Using this syntax would ensure that only a single SkyLounge job could exist.


### SkyLounge steps

The steps field is an array of objects. We declare configurable steps in a workflow template as `skylounge-step: step_name`. Be sure to include the `step_name` declaration in the skylounge.yml.

For example, the step definition specified by `build-artifact` will be inserted into the workflow template below:

```
...
jobs:
  build:
    ...
    steps:
    - uses: actions/checkout@v3
    - skylounge-step: build-artifact
    - uses: actions/upload-artifact@v3
    ...
```
