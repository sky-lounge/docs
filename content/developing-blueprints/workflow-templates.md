---
title: "Workflow Templates"
layout: "docs"
weight: 3
---

Workflow templates are [GitHub workflows](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions) with the addition of `skylounge-jobs`, `skylounge-steps`, and parameters. Anything you can automate in a GitHub workflow, you can automate in a SkyLounge workflow template.

When authoring workflow templates, we first recommend developing a GitHub workflow in a repository. You can then dissect this workflow into a template by identifying:

* Jobs that should be configurable
* Steps that should be configurable
* Configuration to be parameterized 

### SkyLounge Jobs

Jobs can be configurable in a workflow template. To declare a job for configuration, specify the key as name of the job, and `skylounge-job` as the value.

For example, the `build` job in the below workflow template will be replaced:

```
...
jobs:
  build: skylounge-job
```

Include the `build` job declaration in the skylounge.yml.

**Why not `skylounge-job: build`?**

In the GitHub workflow spec, the `jobs` field is a hash or dictionary, not an array. Therefore, you cannot have multiple entries with a key of `skylounge-job`. Using this syntax would ensure that only a single job could be replaced.

Refer to the [Implementing Jobs](/using-blueprints/) section for details on how to write job definitions.

### SkyLounge Steps

The steps field is an array of objects. The objects may be `uses` or `name`. Therefore, we declare configurable steps in a workflow template as `skylounge-step: step_name`. Be sure to include the `step_name` declaration in the skylounge.yml.

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
    ...
```

Refer to the [Implementing Steps](/using-blueprints/) section for details on how to write step definitions.

### Parameters

Parameters are declared in templates using double quote notation: `((a-parameter))`. Parameters starting with an underscore are considered optional: `((_optional_parameter))`.

