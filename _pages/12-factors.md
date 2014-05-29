---
layout: page
title: "12 Factors"
category: guide
date: 2014-05-29 19:36:08
---

The [12 Factor](http://12factor.net/) document outlines best practice
process and policy for system development and operation management. The
goal of this document is to map each factor to an implementation plan for
the Siminars team, its development model, the development repositories
and artifacts and the Siminars.com runtime system.


## Process outline

Code is developed, versioned and tagged using the
[git flow](http://nvie.com/posts/a-successful-git-branching-model/)
process model.

Repo, build, CI and Demo/Staging/Production systems are managed using
puppet and custom deployment scripts.

All apps are packaged as debian dpkg packages. Packages are
named: siminars-*component*_*version*_*build*
    ..* version comes from either date/branch/changeset or release tag


## The Factors

#### I. One codebase tracked in revision control, many deploys

#### II. Explicitly declare and isolate dependencies

#### III. Config Store config in the environment

#### IV. Backing services as attached resources

#### V. Build, release, run strictly separate build and run stages

#### VI. Processes - Execute the app as one or more stateless processes

#### VII. Port binding - Export services via port binding

#### VIII. Concurrency - Scale out via the process model

#### IX. Disposability - Fast startup and graceful shutdown

#### X. Development, staging, and production as similar as possible

#### XI. Logs as event streams

#### XII. Run admin/management tasks as one-off processes
