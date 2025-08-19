---
title: "Building a container image"
teaching: 10
exercises: 10
questions:
- "How can I build a container image with my own code in a GitLab CI/CD pipeline?"
objectives:
- "Know how to build and store a container image in a GitLab CI/CD pipeline"
keypoints:
- "Easy-to-use yml templates exist for container image building"
---

## Build a container image

~~~
stages:
[...]


include:
  - project: 'cms-analysis/general/container-image-ci-templates'
    file:
      - 'kaniko-image.gitlab-ci.yml'


[...]

build_container_image:
  extends: .build_kaniko
  variables:
    DOCKER_FILE_NAME: "Dockerfile"
    PUSH_IMAGE: "true"
    REGISTRY_IMAGE_PATH: "${CI_REGISTRY_IMAGE}:${CI_COMMIT_SHORT_SHA}"

~~~
{: .language-yaml}

{% include links.md %}
