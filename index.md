---
layout: lesson
root: .  # Is the only page that doesn't follow the pattern /:path/index.html
permalink: index.html  # Is the only page that doesn't follow the pattern /:path/index.html
---
The aim of this module is to learn how to run jobs that require a CMS-specific software stack and how to access protected files in GitLab CI using the GitLab installation at CERN.
Participants will run [CMS software (CMSSW)][cmssw] jobs as a case study.

<!-- this is an html comment -->

{% comment %} This is a comment in Liquid {% endcomment %}

> ## Prerequisites
>
> Basic understanding of the purpose of GitLab CI and of its use, as described in the [HSF tutorial][hsf-training-gitlab-ci].
> Basic understanding of using and developing in [CMSSW][cmssw].
{: .prereq}

> ## Learning Objectives
>
> After completing this module, participants will be able to:
>
> - Set up a GitLab CI environment to run CMS software workflows (CMSSW) on CERN infrastructure. 
> - Compile and manage CMSSW packages within GitLab CI pipelines. 
> - Access protected resources by generating and using grid proxies in CI jobs. 
> - Use CMS authentication services to enable secure CI workflows. 
> - Run CMSSW jobs for testing in GitLab CI. 
> - Build container images to create reproducible CMSSW environments in CI/CD pipelines.
{: .objectives}

{% include links.md %}
