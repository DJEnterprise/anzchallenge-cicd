ANZ Challange CI-CD Pipeline Repo
========

Requirements
-----
Would require below pre-requirements to run this pipeline
1. Jenkins configured with required instance role applied to provision AWS Infrastructure
2. Jenkins server installed with git, terraform, docker and awscli.

This contains CI-CD pipeline file for Jenkins for ANZ Challange App

ANZChallengeCI
------

This Job is parameterized and requires Docker Repo URL and Docker Image to run the job.

ANZChallengeCI Pipeline contains following stages:

1. Get Docker Image Version -  This will work out the Docker Image Version based out of source code's latest git commit hash (https://github.com/DJEnterprise/anzchallenge.git). Pipeline will skip the rest of the stages if an image is already built and found in Docker Repo.
2. Code Scan by SonarQube   -  This stage will use sonarqube to analyse the source code. SonarQube Server must be running, if not code scan will be skipped and progressed to source code build stage for this challenge.
3. Build Code               -  This is reponsible for building source code
4. Docker Build and Push to Repo - This stage will build the docker, tags it and pushes the image to Docker repository provided in parameter.

ANZChallengeCD
-------

This Job is parameterized and requires Docker Repo URL, Docker Image and S3 Bucket Name(stores the TF state files) to run the job.

ANZChallengeCD Pipeline contains following stages:

1. Get Docker Image Version -  This will work out the Docker Image Version based out of source code's latest git commit hash (https://github.com/DJEnterprise/anzchallenge.git). This is used to Identify the latest versioned image from Repo.
2. Git Checkout and Terraform Plan - This will checkout the IaC from https://github.com/DJEnterprise/anzchallengeiac.git and Initiates the Terraform pointing to S3 backend and does the Terraform Plan
3. Terraform Approva        -  This will display the output from Terraform Plan and Prompts user to approve in Jenkins before go-ahead with the Apply.
4. Terraform Apply          -  This will do the Terraform apply to build the Infrastructure and deploy the application to Kubernetes cluster.

ANZChallengeDestroy
--------

This Job is parameterized and requirees S3 Bucket Name to run the job.

ANZChallengeDestroy pipeline will destroy the infrastructure created for ANZChallenge app.