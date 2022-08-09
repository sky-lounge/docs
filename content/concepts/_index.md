---
title: "Core Concepts"
icon: "far fa-file-code"
description: "An overview of the basic constructs and goals of SkyLounge"
type : "docs"
weight: 2
---

SkyLounge extends GitHub Actions and Workflows to make automation more efficient, reusable, and maintainable. SkyLounge is built on three main constructs:
            
* **Blueprint**: Defines the automation applied to a repository. Blueprints leverage one or more workflow templates.
* **Workflow Template**: A SkyLounge extension to GitHub workflows bringing composability and reusability. All features and functions available in GitHub workflows are available in workflow templates.
* **skylounge.yml**: Repository-specific configuration, controlled by developers and stored in the root of the repository, directing SkyLounge on how to apply the blueprint used in that repository.
            
### Blueprints

A blueprint is a yaml file specifying one or more workflow templates to be applied to repositories. Blueprints define _all_ of the automation, not just CI/CD. Automation to handle pull requests, projects, issues, or any capability of GitHub is possible in SkyLounge. Blueprints are stored in a GitHub repository, registered with SkyLounge, with a name ending in `skylounge-definitions`. This `skylounge-definitions` repository can serves as a shared library of workflows, jobs, and steps as you will see below.

### Workflow Templates

SkyLounge extends GitHub workflows with three capabilities:

* **skylounge-job**: The ability to define [jobs](https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow) to be provided by development teams.
* **skylounte-step**: The ability to define [steps](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idsteps) to be provided by the development teams.
* **parameters**: The ability to inject parameters/configuration into workflow files at three levels: job, workflow, or blueprint.

### skylounge.yml

The skylounge.yml is the single configuration file developers use to "wire up" a blueprint. It lives in the repository along with the code base. 


