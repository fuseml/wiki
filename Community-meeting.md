# First Community meeting


## Goals

Introduce FuseML to wider community, educate about its purpose and architecture.

## Basic script for the meeting


### Introduction [Stefan]

Welcome, state the goals of the meeting and agenda (very briefly)

### Presentation/Demo [Stefan]

We could include same Demo Stefan/Alessandro already offered in different meetings to get people on the same page.

### Installer [Jiri]

Quick demonstration of the installer:

  - mention that it is a fork of carrier project (meanwhile renamed to Epinio)
    - https://github.com/epinio/epinio
  - explain that it needs Kubernetes cluster with specific setup
  - list the components parts it is installing
  - show the installation

### Brief architecture overview [Stefan]

  (Unless it was already part of initial presentation point)

  - Client-server architecture
  - How it fits into MLOps mindset
  - dependency on other components (mentioned by installer)
  - show current components (more details to follow)


### Concepts: Codesets [Jiri]

  - "sets of code", basically the Data Scientist's project
  - currently implemented as git(ea) repositories
  - show commands live:
    - create a codeset (+ show gitea web UI too)
    - list codesets
    - delete codeset

### Concepts: Applications [Flavio]

  - the result of the workflow/pipeline
  - show commands live
    - show list of applications
    - test prediction service using the output URL from application
    - (show the web app?)
    - delete an application

### Concepts: Worfkflows [Flavio]

  - This is the main component, driving MLOps work routine
  - explain DSL, describe example yaml file
  - currently implemented as Tekton pipelines
  - explain relation with
    - codesets
    - workflow runs
    - applications
  - show commands live
    - create workflow
    - list codesets and assign workflow to codesets
    - show workflow runs (+ show Tekton dashboard)
  
### Q&A

  - it is possible (though unlikely) some people will be there

### Closing Time [Stefan]

  - point to

    - github repository
    - issues for feedback
    - Slack channel
