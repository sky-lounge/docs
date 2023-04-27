---
title: "SkyLounge Basics"
icon: "far fa-file-code"
description: "The constructs, principles, and process of working with SkyLounge."
type : "docs"
weight: 2
---

SkyLounge is integrated with [GitHub](https://github.com) to provide the most natural, intuitive, and noninvasive developer experience possible. SkyLounge integrates with GitHub through the [GitHub application](https://docs.github.com/en/apps) API. 

<br />

### Why GitHub?
Git is the industry standard source management system. It is the overwhelming platform of choice for organizations building software. As a natural extension to the GitHub platform, SkyLounge provides a simple and cost-effective path to better automation. 

SkyLounge leverages commonly used capabilities of GitHub. These are tools many developers already know. At its core, SkyLounge extends the [GitHub Actions platform](https://docs.github.com/en/actions). SkyLounge is purely additive. Anything you can do in GitHub actions, you can do in SkyLounge.

![GitHub capabilities](/images/github-capabilities.png "github capabilities")
<sub>SkyLounge is built on GitHub using familiar tools.</sub>

SkyLounge leverages [Pull Requests](https://docs.github.com/en/pull-requests) and [Issues](https://docs.github.com/en/issues). Pull requests and issues are common tools used by most developers on the GitHub platform. Pull requests are used to suggest changes to automation, keeping control squarely in the developer's hands. Errors in automation generation (like missing configuration) are reported via issues, clearly outlining the issue and how to fix it. SkyLounge is one of the team an automation specialist hyperfocused on keeping full lifecycle automation up to date.

<br />

### First principles

As you get more familiar with SkyLounge, it can be helpful to understand the first principles we use in building and evolving SkyLounge.

**Complement your flow**: SkyLounge blends into your flow and effectively fades into the background during development. It is like an extra expert on each and every team. Skylounge works the way you work, leveraging GitHub capabilities whenever possible.

**Keep developers in control**: SkyLounge keeps developers in control, not just of application-specific automation, but the entire process. For this reason, SkyLounge never commits directly to the main branch of repositories. Instead, it uses branches and pull requests to show developers what it would like to do. 

**Reuse, reuse, reuse... unless you can't**: There is so much duplication in automation across software teams. SkyLounge aims to remove that. However, there are always exceptions. SkyLounge should never stand in the way of what you need to do, whatever the reason.

**Knowledge is power**: SkyLounge strives to communicate the complex process of managing automation across an organization in clear and concise language. It also builds shared understanding. With SkyLounge, you can make informed decisions at every level. 