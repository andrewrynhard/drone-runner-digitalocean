---
date: 2000-01-01T00:00:00+00:00
title: Environment
author: bradrydzewski
weight: 8
toc: false
description: |
  Configure pipeline environment variables.
---

Drone provides the ability to define environment variables scoped to individual build steps. Example pipeline step with custom environment variables:

{{< highlight text "linenos=table,hl_lines=16-18" >}}
kind: pipeline
type: digitalocean
name: default

token:
  from_secret: token

steps:
- name: build
  commands:
  - go build
  - go test
  environment:
    GOOS: linux
    GOARCH: amd64
{{< / highlight >}}

Drone automatically injects environment variables containing repository and commit metadata into each pipeline step. See the environment variable index for a full list of variables.

{{< link "/configuration/variables" >}}

# Secrets

Drone provides the ability to source environment variables from secrets. In the below example we provide the username and password as environment variables to the step.

{{< highlight text "linenos=table,linenostart=8,hl_lines=8-11" >}}
steps:
- name: build
  commands:
  - docker login -u $USERNAME -p $PASSWORD
  - docker build -t hello-world .
  - docker push hello-world
  environment:
    PASSWORD:
      from_secret: password
    USERNAME:
      from_secret: username
{{< / highlight >}}

# Common Problems

Please note `${variable}` expressions are subject to pre-processing. If you do not want the pre-processor to evaluate your expression it must be escaped.

{{< highlight text "linenos=table,hl_lines=5,linenostart=5" >}}
steps:
- name: build
  commands:
  - echo $GOOS
  - echo $${GOARCH}
  - go build
  - go test
{{< / highlight >}}

Please also note the environment section cannot expand environment variables or evaluate shell expressions. If you need to construct variables it should be done in the commands section.

{{< highlight text "linenos=table,hl_lines=4,linenostart=5" >}}
steps:
- name: build
  commands:
  - export GOPATH=$HOME/golang
  - go build
  - go test
{{< / highlight >}}
