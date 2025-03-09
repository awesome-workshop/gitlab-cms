---
title: "CAT services for GitLab CI"
teaching: 10
exercises: 15
questions:
- "How can I more easily access CMS resources in GitLab CI?"
objectives:
- "Demonstrate the use of the CAT EOS file service"
- "Demonstrate the use of the CAT VOMS proxy service"
keypoints:
- "To use CAT services your project needs to reside into [cms-analysis](https://gitlab.cern.ch/cms-analysis)."
- "You won't need to expose any personal credentials."
- "It is easy to host your analysis code in [cms-analysis](https://gitlab.cern.ch/cms-analysis)."
---
{% include links.md %}

## The [cms-analysis][cms-analysis] user code space

The Common Analysis Tools (CAT) group in CMS maintains a CERN GitLab area called [cms-analysis][cms-analysis], where anyone in CMS can store their analysis code.
The area is documented in the [CAT documentation pages][cat-docs].
The area is organized in groups and subgroups, following the CMS Physics Coordination group structure.
You can request the creation of an area in the PAG-specific group that best matches your analysis.

> ## You can request an area for your analysis at any time
> Bear in mind that it is always a good idea to keep you analysis code under version control.
> At any stage in your analysis you can request an area for your code.
> In fact we invite you to do so.
> The area can be created with a temporary name, which can then be changed to match the CADI line, when your analysis is mature enough to have one.
{: .callout}

> ## The services described here only work in [cms-analysis][cms-analysis]
> For security reasons, the services described in the following only work if your project is in the `cms-analysis` namespace.
> You can move the project you have been using so far in this lesson to the `cms-analysis` namespace by going to `Settings` --> `General` --> `Advanced` --> `Transfer project` and select `cms-analysis / CMSDAS / CAT-tutorials` as a new namespace.
The `cms-analysis / CMSDAS / CAT-tutorials` is to be used for the purpose of testing.
You should in general select a target namespace in the relevant POG/PAG subgroups.
{: .callout}

## Using the CAT EOS file service
CAT has a service account, `cmscat`, that is in the _zh_ group and is a member of the CMS VO. CAT provides a service to request an EOS token in a GitLab CI job to be able to access CMS files on EOS on behalf of the `cmscat` service account.

The service is described in more detail in [here](https://cms-analysis.docs.cern.ch/code/services/#usage-of-gitlab-ci-dataset-service).

The files accessible through this method are hosted in `/eos/cms/store/group/cat`.

> ## The file you need is not there?
> You can request more datasets to be stored in `/eos/cms/store/group/cat` by creating a MR to [https://gitlab.cern.ch/cms-analysis/services/ci-dataset-files/-/blob/master/datasets.txt](https://gitlab.cern.ch/cms-analysis/services/ci-dataset-files/-/blob/master/datasets.txt).
{: .callout}



> ## Exercise: setup a CI job the copies a file using the CAT EOS file service
>
> There is a few technical aspects that are involved in this.
> First, your GitLab CI job needs to be configured to that it creates an authentication token.
> This is achieved with the following lines:
> ~~~
> id_tokens:
>     MY_JOB_JWT:
>        aud: "cms-cat-ci-datasets.app.cern.ch"
> ~~~
> Second, you need to query a service, hosted at `https://cms-cat-ci-datasets.app.cern.ch`, to give you a short lived token to access a file on EOS, on behalf of the `cmscat` service account.
This is achieved with the following lines:
> ~~~
> XrdSecsssENDORSEMENT=$(curl -H "Authorization: ${MY_JOB_JWT}" "https://cms-cat-ci-datasets.app.cern.ch/api?eospath=${EOSPATH}" | tr -d \")
> ~~~
> Where `EOSPATH` is a variable holding a path of a file on EOS.
> Now you can access the file with a path that includes the newly generated token at the end, as: `root://eoscms.cern.ch/${EOSPATH}?authz=${XrdSecsssENDORSEMENT}&xrd.wantprot=unix`.
Try copying the file: `/eos/cms/store/group/cat/datasets/MINIAODSIM/RunIISummer20UL17MiniAODv2-106X_mc2017_realistic_v9-v2/DYJetsToLL_M-50_TuneCP5_13TeV-amcatnloFXFX-pythia8/2C5565D7-ADE5-2C40-A0E5-BDFCCF40640E.root`
{: .challenge}

> ## Solution
> A possible solution to the challenge above is the following:
> ~~~
test_eos_service:
  image:
    name: registry.cern.ch/docker.io/cmssw/el7:x86_64
  tags:
    - cvmfs
  id_tokens:
    MY_JOB_JWT: # or any other variable name
        aud: "cms-cat-ci-datasets.app.cern.ch"
  variables:
    # File is taken from https://cms-cat-ci-datasets.web.cern.ch/
    EOSPATH: '/eos/cms/store/group/cat/datasets/MINIAODSIM/RunIISummer20UL17MiniAODv2-106X_mc2017_realistic_v9-v2/DYJetsToLL_M-50_TuneCP5_13TeV-amcatnloFXFX-pythia8/2C5565D7-ADE5-2C40-A0E5-BDFCCF40640E.root'
    EOS_MGM_URL: root://eoscms.cern.ch 
  before_script:
  - 'XrdSecsssENDORSEMENT=$(curl -H "Authorization: ${MY_JOB_JWT}" "https://cms-cat-ci-datasets.app.cern.ch/api?eospath=${EOSPATH}" | tr -d \")'
  script:
    - xrdcp "${EOS_MGM_URL}/${EOSPATH}?authz=${XrdSecsssENDORSEMENT}&xrd.wantprot=unix" test.root
    - ls -l test.root
> ~~~
> {: language-yaml}
{: .solution}

## Using the CAT VOMS proxy service

{% include links.md %}
