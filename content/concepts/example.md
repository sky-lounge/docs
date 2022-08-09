---
title: "Example"
layout: "docs"
weight: 2
---

To illustrate the power of SkyLounge, let's discuss a blueprint to deploy web applications to Google Cloud Run. This blueprint would likely contain workflow templates to automate development, promotion, production, pull requests, issues, etc. 

Let's dive into a `development` workflow. What would we want this to do for each web application? Perhaps:

1. Run unit tests
1. Build a container image
1. Virus scan the container image
1. Push the container image to Google Artifact Registry
1. Deploy the container image to Cloud Run
1. Run integration tests
1. Run penetration tests
1. Mark the container image as "ready for promotion"

You may have a different list of tasks. But for this example let's go with it and break each down.

**1. Unit Tests**: Running unit tests is language/technology-specific, not application specific. The runtime configuration for tests will also be language-dependent.

Therefore, a customer could define a job or step to run unit tests for each technology they use and store it in their `skylounge-definitions` repository. For example, a customer using Java and Gradle wrapper could define a skylounge job or step that sets up the Java environment and runs `./gradlew test`. Developers could then select the technology stack they are using by referencing the shared definition file in the `skylounge-definitions` repository in their `skylounge.yml` configuration.

**2. Building container images**: Building a container image is also not typically application specific. Developers are likely using docker or buildpacks. Therefore, the organization can add a `docker build` step or job to their `skylounge-definitions` repository. Or, if the organization is only using buildpacks or docker, they can implement the build task in the workflow template (no developer config needed).

Not so fast... When we build a container image, we have to assign a name and a version. How many times do you see divergent versioning and naming between teams? SkyLounge provides a great opportunity to standardize this naming by implementing it in the workflow template. The organization may choose semantic versioning or choose to tie the version to the git commit of the latest change in the repository. Either way, this not only yields consistency but also traceability of what image has been deployed. 

**3. Virus Scanning container images**: This is a common task shared by all. Therefore, it should be implemented in the workflow template file. This ensures known viruses are caught _before_ they are pushed to the Artifact Registry.

But seriously, how many of your development teams are doing this now? With SkyLounge, the answer can be "100%".

**4. Pushing to Google Artifact Registry**: This is a common task implemented in the workflow template.

**5. Deploy Cloud Run**: This is a common task implemented in the workflow template.

**6. Integration Tests**: This is application-specific. When using SkyLounge, development teams typically implement an `integration-test` job and store this in the repository.

**7. Penetration Tests**: This is a common task implemented in the workflow template.

**8. Mark the container image as "ready for promotion"**: Like versioning and naming schemes, this is often divergent across teams. However, now is a great time to create shared understanding and standardize this by implementing it in the workflow template.

### Summary Point of View

Let's summarize the actions and impact of this single workflow template in SkyLounge from three perspectives.

**Developers:**

* Configure application-specific parameters and secrets (domains, credentials, etc.) using GitHub secrets and SkyLounge parameters.
* Configure their application stack in the `skylounge.yml` by referencing shared automation for running unit tests and building the container image.
* Implement and maintain application-specific integration tests. Nothing more.

**Engineering Managers:**

* Can look across the org and know that all automation is centrally managed and kept up to date.
* Can look across teams at consistent naming, versioning, and traceability.
* Know that any developer in the org can easily reason over _every_ other repository's automation.

**Security and Compliance:**

* Rest assured that every container image is scanned for viruses _before_ it reaches persistent storage.
* Rest assured that every web application is penetration tested _before_ it is deployed to production.
* Have a clear location to add and roll out additional scans and security automation as needed.

Of course, there are many more workflows in a comprehensive blueprint. But hopefully, this helps illustrate the power of SkyLounge. In summary, SkyLounge lets **developers focus on code**, **organizations efficiently create shared understanding**, and **security teams secure the full lifecycle for all applications**.