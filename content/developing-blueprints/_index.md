---
title: "Developing Blueprints"
icon: "fa-solid fa-cubes"
description: "Build your automation and share it across your organization."
type : "docs"
weight: 5
---

If you can author GitHub workflows, you can author SkyLounge blueprints. Blueprints are stored in a GitHub repository named `skylounge-definitions` in your GitHub organization. Any user within your organization will have access to use these blueprints.

To add your blueprints to SkyLounge:

* Create a repository in your GitHub organization named `skylounge-definitions`
* In that repository, create a directory named `blueprints`

The files for each blueprint should be stored in the `blueprints` directory in a folder corresponding to the id of blueprint. For example, if you want to create a hello world blueprint, you should create a folder called `hello-world` in the `blueprints` directory of your repository.

### Registering your definitions repository

Register your `skylounge-definitions` repository with through the GitHub interface the same way you would register an application. To do this:

* Go to your organization in GitHub and click "Settings"
* Under "Integrations" on the left-hand side, click "GitHub Apps"
* Next to SkyLoungeio, click "Configure"
* Under "Repository access", click "Select repositories"
* Search for and select your `skylounge-definitions` repository
* Click "Save"

Your blueprints will now show up in the dashboard.

