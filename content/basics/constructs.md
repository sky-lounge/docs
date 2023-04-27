---
title: "Constructs"
layout: "docs"
weight: 3
---

SkyLounge extends the GitHub Actions platform to bring efficiency, reusability, and security to automation.

<br />

### Roles

While SkyLounge is designed to support the entire organization's needs, two types of users interact most with the platform.

* **Platform team**: The Platform team builds and maintains automation that meets the organization's CI/CD, security, and compliance requirements. 

* **Developers**: Developers authorize SkyLounge, select their automation profile, and provide app-specific configuration and automation.

The following sections detail SkyLounge concepts in the context of these two roles.

<br />

### SkyLounge libraries

A **SkyLounge Library** is a special GitHub repository that houses shared automation for an organization. The platform team owns this repository.  

![Platform concepts](/images/platform-concepts.png "platform concepts")

The SkyLounge library contains:

* **Blueprints**: A blueprint defines the automation to be applied to a repository. A blueprint contains one or more workflow templates. Typically, an organization will have a blueprint for each type of application. Examples: webapps, worker apps, iOS or Android apps, etc.
* **Workflow templates**: A workflow template is a SkyLounge extension to GitHub Workflows, bringing composability and reusability. A workflow template enables the inclusion of job and step definitions.
* **Job and step definitions**: These are an extension of GitHub jobs and steps, respectively. Again, job and step definitions enable the composability and reusability of automation.
* **Blueprint profiles**: A blueprint profile defines what blueprint to apply to a repository and how to apply the blueprint. Labels on a repository drive blueprint profiles. For example, a blueprint profile can be used to select the "webapp" blueprint and then apply language and infrastructure-specific job and step definitions.

SkyLounge identifies a repository as a SkyLounge library via the name. While any name ending in `skylounge-library` will be treated as a library, we suggest using the name `skylounge-library` rather than `a-skylounge-library`. Blueprint profiles must be stored in a repository named `skylounge-library`.

#### Library example

We can map our earlier development workflow example to the above constructs in a SkyLounge library. *Note: we will discuss blueprint profiles later.*

![SkyLounge library example](/images/library-example.png "SkyLounge library example")

The develop workflow becomes a workflow template, referenced in a blueprint. Organizational automation is implemented directly in the development workflow template. The stack/technology automation is extracted into reusable job and/or step definitions. The use of a particular stack or technology is discussed below.

<br />

### Managed repositories

A **Managed Repository** is any repository in GitHub for which SkyLounge manages automation. Typically this will be an application code repository; however, SkyLounge can also be used to manage infrastructure or any other automation. 

![Managed Repository](/images/managed-repository.png "managed repository")

In addition to your code, the managed repository will include:

* **Application-specific job and step definitions**: Application-specific automation like integration tests will be implemented as job or step definitions and stored in the repository.
* **skylounge.yml**: The skylounge.yml is a single configuration file at the repository's root that directs SkyLounge. It specifies the blueprint to use, the configurable job and step definitions, and parameters/configurations specific to the application.

Because the platform team manages shared automation, developers are free to only focus on application-specific automation and configuration. Any repository without a name ending in `skylounge-library` is treated as a managed repository.
