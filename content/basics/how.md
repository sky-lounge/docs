---
title: "How it works"
layout : "docs"
weight: 4
---

### 1. Blueprints define automation

A blueprint defines the automation applied to a repository. Blueprints are shared across the organization. They describe the core automation and the application-specific automation provided by developer teams. Blueprints create shared understanding, remove duplication, and go far beyond just CI/CD.

![Sample blueprint](/images/blueprint.png "sample blueprint")

<br />

### 2. Managed repository configuration

A single configuration file, skylounge.yml, is checked into each managed repository. It defines the repository-specific configuration, including the repository-specific automation inserted into the blueprint. The skylounge.yml keeps control in the developer's hands.

![Sample skylounge.yml](/images/skyloungeyml.png "sample skylounge.yml")

<br />

### 3. Comprehensive automation

SkyLounge generates comprehensive automation, merging the commonality of the blueprint with customizations specified in the skylounge.yml. The result is full-lifecycle automation for your repository. And to ensure developers maintain control, SkyLounge will always open a pull request rather than making changes directly. Full-featured, full-control, full-lifecycle.

![Sample pull request](/images/pullrequest.png "sample pull request")

<br />

### 4. Always up to date

SkyLounge monitors the inputs for each managed repository (blueprints, workflow template, job & step definitions, and skylounge.yml). When any changes are detected, SkyLounge opens a new pull request. There is no need for each team to implement that new security scan or update their build tools.