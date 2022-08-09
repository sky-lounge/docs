---
title: "blueprint.yml"
layout: "docs"
weight: 2
---


Each blueprint directory must contain a blueprint definition file named blueprint.yml. This file specifies the blueprint's workflow templates and all of the documentation. The hello-skylounge blueprint can serve as a reference.

The `blueprint.yml` has the following structure:

```
id: # The ID of the blueprint. This is unique in your repository. 
    # It must match the directory name (i.e. `hello-world`).

github-org: # Your github organization where this repository 
            # resides.

github-repo: # The name of your `skylounge-definitions` repository. 
             # For now, this needs to be `skylounge-definitions`. 
             # We are working on extending this model.

display-name: # The display name shown in the dashboard for 
              # this blueprint

description: |
  # A description of this blueprint, shown on the main screen of 
  # the dashboard at the `/blueprints` endpoint. This field 
  # may contain markdown.

documentation: |
  # Detailed documentation shown in the dashboard when clicking 
  # the blueprint details link. This field may contain markdown.

tags: # Tags are a way of providing a quick visual reference to 
      # blueprint users. An array of tags may be specified. Current 
      # valid tags are:
  # - gcp
  # - aws
  # - azure
  # - demo
  # - container-build
  # - virus-scan
  # - penetration-test

workflows:  # An array of one or more workflows that will be 
            # generated when using this blueprint. 
  
  - workflow: # The name of the workflow. Example: hello
  
    description: | 
      # A description of this workflow, shown on the main 
      # screen of the dashboard at the `/blueprints` 
      # endpoint. This field may contain markdown.

    documentation: |
      # Detailed documentation shown in the dashboard when 
      # clicking the workflow details link. This field 
      # may contain markdown.
    
    template-uri: # The URI of the workflow template in 
                  # GitHub. Example: 
                  # sky-lounge/public-skylounge-definitions/blueprints/hello-skylounge/hello.yml
    
    branch: # The branch this workflow is for. SkyLounge 
            # is "branch aware" as GitHub requires workflows 
            # to exist in the branch the workflow is 
            # for. Example: main

    icon: # An icon code from either 
          # https://icons.getbootstrap.com/ or the 
          # free https://fontawesome.com/icons icon set 
          # to display for this workflow. Example: fa-solid fa-hands
```
