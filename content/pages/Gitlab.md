---
tags:
- Gitlab
title: Gitlab
categories:
date: 2022-09-29
lastMod: 2022-09-29
---


# CI/CD

  + ## Pipeline

    + ### Skip a pipeline

      + To push a commit without triggering a pipeline, add  `[ci skip]`  or  `[skip ci]` , using any capitalization, to your commit message.

      + Alternatively, if you are using Git 2.10 or later, use the  `ci.skip`  [Git push option](https://docs.gitlab.com/ee/user/project/push_options.html#push-options-for-gitlab-cicd). The  `ci.skip`  push option does not skip merge request pipelines.

        + `git push --push-option=ci.skip` or `git push -o ci.skip`.




