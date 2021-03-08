# Deployment Pipelines

## Introduction
This repository hosts a dedicated pipeline for deployment jobs to staging and production environments.

There is also an additional stage which can optionally purge corresponding service relevant dns cache on Cloudflare.

## Usage:
Other projects of this GitLab instance can optionally trigger this pipeline remotely, and consequently deploy their changes to whichever environment they choose.


This remote trigger happens by adding below job and its relevant stage to their gitlab-ci file.


<pre>
<code>trigger_remote_deployment_pipeline:
   stage: deploy
   image: curlimages/curl:7.71.1
   rules:
     - if: '$CI_COMMIT_BRANCH == "staging"'
       when: always
     - if: '$CI_COMMIT_BRANCH == "master"'
       when: always
     - when: never
   script:
     - curl -F "token=$DEPLOYMENT_PIPELINE_TRIGGER_TOKEN" -F "ref=master" -F "variables[PURGE_CLOUDFLARE_DNS_CACHE]=true" -F "variables[K8S_STAGING_NAMESPACE]=$K8S_STAGING_NAMESPACE" -F "variables[K8S_PRODUCTION_NAMESPACE]=$K8S_PRODUCTION_NAMESPACE" -F "variables[K8S_DEPLOYMENT]=$K8S_DEPLOYMENT" -F "variables[K8S_DEPLOYMENT_CONTAINER]=$K8S_DEPLOYMENT_CONTAINER" -F "variables[IMAGE_NAME]=$IMAGE_NAME" -F "variables[IMAGE_TAG]=$IMAGE_TAG" -F "variables[COMMIT_BRANCH]=$CI_COMMIT_BRANCH" -F "variables[PROJECT_PATH]=$CI_PROJECT_PATH" -F "variables[IMAGE_REPOSITORY]=$IMAGE_REPOSITORY" -X POST --url https://git.tapsell.ir/api/v4/projects/567/trigger/pipeline</code>
</pre>



