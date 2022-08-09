---
title: "skylounge.yml"
layout: "docs"
weight: 4
---

Now that you have defined your blueprint and workflow templates, you want to make it easy for a user to configure their repository to use your blueprint. Providing a highly commented `skylounge.yml` file in the same directory as the `blueprint.yml` file will enable users to download it in the dashboard.

The `skylounge.yml` should be well documented with respect to intent. Again, you can use [hello-skylounge](https://github.com/sky-lounge/public-skylounge-definitions/blob/main/blueprints/hello-skylounge/skylounge.yml) as an example.

The `skylounge.yml` has the following structure:

```
blueprint-uri: # The URI of the blueprint.yml file. Example: 
               # sky-lounge/public-skylounge-definitions/blueprints/hello-skylounge/blueprint.yml

workflows: # An array of workflows (templates) that require 
           # configuration. If you have a workflow that does
           # not require any user configuration, you do not
           # need to include it here.

- workflow: # The name of the workflow. Example: hello
  
  jobs: # An array of jobs that require configuration.
        # If the job and its steps do not require 
        # configuration, you do not need to include 
        # it here.

    - job: # The name of the job in the workflow template. 
           # Example: say-hello

      steps: # An array of steps that require configuration.
             # If the step does not require configuration, 
             # you may omit it.

        - step: # The name of the skylounge-step to be 
                # configured. This much match the name 
                # used with the `skylounge-step` tag
                # in the workflow template.
                # Example: say-hello
          
          template-uri: # To be provided by the developer. 
                        # This is the URI of the step definition
                        # they will be using.

      params: # Optional: an dictionary of parameters used by the 
              # job as key-value pairs
        key: # User to specify the value

      # The following block is a skylounge-job, to be 
      # configured by the user.
      - job: integration-test
        template-uri: # The URI to the integration test job
                      # definition in GitHub. To be provided by the 
                      # user.

    params: # This parameter block is at the workflow level.
      key: # Value provided by the user.

params: # This parameter block is global for the blueprint.
  key: # Value provided by the user.
```

We recommend looking at the Google Cloud Run blueprint in the dashboard for a comprehensive example of a `skylounge.yml` file. This is the blueprint used by the SkyLounge dashboard application.

