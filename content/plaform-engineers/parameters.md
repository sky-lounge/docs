
---
title: "Parameters"
layout: "docs"
weight: 2
---

SkyLounge parameters extend the GitHub Actions syntax to enable configuration through a skylounge.yml file. Parameters are defined in workflow_templates and job/step definitions using double parentheses notation: `((parameter))`. Parameter names that start with an underscore (`_`) are optional and can be omitted. For example, `((_optional_parameter))` can be omitted.

Parameters can be set at the blueprint, workflow, job, or step level. The most specific parameter will be applied first (step before job before workflow before blueprint).
