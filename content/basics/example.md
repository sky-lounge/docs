---
title: "SkyLounge by example"
layout: "docs"
weight: 2
---

Before we dive into the details, let's look at a simple workflow automating a development environment. This example will help you understand the power of automation with SkyLounge. 

![Sample development workflow](/images/dev-workflow.png "sample development workflow")
<sub>A simple workflow to manage the development phase of an application.</sub>

* **Organization**: Automation should be the same across the organization, regardless of the underlying technology or stack. Example: Naming and versioning.
* **Stack/technology**: Automation that is technology or stack specific (think language or deployment platform, etc). Automation for a given stack or technology should be the same across the organization. Example: Deployment to Kubernetes.
* **Application**: Automation that is specific to the application. Example: Integration tests.

We can dissect the development workflow into these categories. 

![Development workflow characterized](/images/dev-workflow-characterized.png "development workflow characterized")
<sub>Dissected development workflow by organization, stack/technology, and application-specific concerns.</sub>

You may dissect the above slightly differently, and that is okay. SkyLounge allows you to build automation and implement your opinions. But regardless, very little above should be in the hands of each development team. 

> _**How much more comprehensive, efficient, and secure can your organization be with SkyLounge?**_

<br />

### Organizational impact

The following section will explore the constructs that enable full lifecycle, efficient, and secure automation. But before we do, let's summarize the impact of approaching this single workflow from the same three introductory perspectives.

*Developers:*

* Implement and maintain application-specific integration tests. Nothing more.
* Configure application-specific parameters and secrets.
* Configure stack/technology-specific selections as enabled by the platform team.

*Engineering Managers:*

* Can look across the org and know that all automation is centrally managed and kept up to date.
* Can look across teams at consistent naming, versioning, and traceability.
* Know that any developer in the org can easily reason over _every_ other repository's automation.

*Security and Compliance:*

* Rest assured that every container image is scanned for viruses _before_ it reaches persistent storage.
* Rest assured that every web application is penetration tested _before_ it is deployed to production.
* Have a clear location to add and roll out additional scans and security automation as needed.

Of course, there are many more workflows in comprehensive automation. But hopefully, this helps illustrate the power of SkyLounge. In summary, SkyLounge lets **developers focus on code**, **organizations efficiently create shared understanding**, and **security teams secure the entire lifecycle for all applications**.

