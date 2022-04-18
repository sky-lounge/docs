### Jobs

Jobs can be replaced in a workflow. To declare a job for replacement in a workflow, specify the key as name of the job, and `skylounge-job` as the value.

For example, the `build` job below will be replaced:

```
...
jobs:
  build: skylounge-job
```

#### Why not `skylounge-job: build`?

In the GitHub workflow spec, the `jobs` field is a hash or dictionary, not an array. Therefore, you cannot have multiple entries with a key of `skylounge-job`. Using this syntax would ensure that only a single job could be replaced.

