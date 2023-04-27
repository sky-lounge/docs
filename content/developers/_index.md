---
title: "SkyLounge for Developers"
icon: "fa-solid fa-cube"
description: "Using SkyLounge to manage automation for your repository."
type : "docs"
weight: 3
---

Before diving in, please be sure you have familiarized yourself with the [Basics](/basics/), especially the [Constructs](/basics/constructs). Below we outline the process of onboarding your managed repository. Once complete, SkyLounge will continue to open pull requests with updates to automation and issues should automation updates fail.

### Enable issues

SkyLounge uses [GitHub Issues](https://docs.github.com/en/issues) to provide feedback on errors. Please be sure Issues are enabled on your managed repository before onboarding. You can do this in GitHub by going to `Settings` > `Features` on your repository and selecting `Issues`.


<br />

### Onboarding managed repositories

We strive to simplify the process of onboarding managed repositories. You can onboard managed repositories by adding [labels](https://docs.github.com/en/issues/using-labels-and-milestones-to-track-work/managing-labels) to the repository when using blueprint profiles. Otherwise, you can onboard applications manually. The net result is the same.

#### 1. Onboarding with blueprint profiles

If your platform team has set up blueprint profiles, they will provide guidance on the available labels (you may need to edit the [labels](https://docs.github.com/en/issues/using-labels-and-milestones-to-track-work/managing-labels) on your repository). SkyLounge will generate a `skylounge.yml` file in a pull request. You will need to edit this pull request to fill in repository-specific details. 

#### 2. Manual blueprint selection

Alternatively, you can choose the appropriate blueprint for your application through the [dashboard](https://dashboard.skylounge.io) available at [https://dashboard.skylounge.io](https://dashboard.skylounge.io). The [blueprints](https://dashboard.skylounge.io/blueprints) page provides documentation for the blueprints you can access. 

From this page, you can view the documentation for the blueprint, and all included workflow templates. You can also view and download the `skylounge.yml` for each blueprint. Once you have chosen the appropriate blueprint, download the associated `skylounge.yml` file and save it at the root of your repository.

<br />


### skylounge.yml configuration

Regardless of your onboarding method, you must edit the `skylounge.yml` file for each managed repository. The skylounge.yml is a single configuration file at the repository's root that directs SkyLounge. It specifies the blueprint to use, the configurable job and step definitions, and parameters/configurations specific to the application. Whether you onboard manually or via repository labels, you must edit this file.

Depending on the blueprint, you may need to configure job definitions, step definitions, and/or parameters. Below is a sample `skylounge.yml` showing the possible configuration inputs. 

```yaml
blueprint-uri: example-org/public-skylounge-library/blueprints/example/blueprint.yml
workflows:
  - workflow: dev-example
    jobs:
      - job: setup
        uri: # Job URI for `setup`. Example: example-org/our-app/skylounge/jobs/setup.yml
        params:
          setup-param: # Value for parameter `setup-param` in step `setup`.
      - job: main
        steps:
          - step: custom-step
            uri: # Step URI for `custom-step`. Example: example-org/our-app/skylounge/steps/custom-step.yml
        params:
          main-param: # Value for parameter `main-param` in job `main`.
params:
  global-param: # Value for global parameter `global-param`.
```

You should be able to follow the comments to complete the configuration. 

#### Configuring jobs and steps definitions

Jobs and steps definitions are referenced by URI, pointing to a file in GitHub. Job and step definitions may reside in your managed repository (as in the case of integration tests), or in your organization's `skylounge-library` (in the case of platform team-managed automation).

When referencing a shared job or step definition in a library repository, pay special attention to any additional parameters. You may need to add configuration for these paramters in your `skylounge.yml`. Should you forget, do not fret. SkyLounge will open an issue highlighting the missing values. 

