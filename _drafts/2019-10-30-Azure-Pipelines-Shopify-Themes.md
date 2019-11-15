---
layout: post
title: Using Azure Pipelines To Deploy Shopify Themes
category: "Rovani in C&sharp;"
excerpt_separator: <!--more-->
tags:
- shopify
- azuredevops
- pipelines
---

Lately, I have been on a pair of Shopify projects, and one of the problems that has already come up with both of them (and I suspect is a recurring problem) is that two people can't work on editing a Theme through the administrative UI at the same time. There are concurrency problems when two people accidentally start editing the same file - which is extremely common for JavaScript and SCSS assets. In coding, this is, what I like to call, a _Solved Problem_. While there are certainly challenges that come along with it, source control is a staple of the development process. In my infinite naïveté, I assumed that I could just create a simple workflow and presto-change-o we have repo-based-development.

1. Stash the theme files into a code repository
2. Whip up a deploy script to copy the files to a public folder
3. Make some calls to the Shopify API
4. Write a blog post

![How To Draw an Owl](/images/draw-an-owl.png){: .boxed }
{: .centered }

<!--more-->

## Step 1: Get the Theme into Source Control

This turned out to be really easy. Shopify has a UI to export the files in a theme. The workflow sends you an email, which contains a link with a Zip file. Download and unzip the file, then push the files up to the repository. For this process, I am using an Azure DevOps private git-based repository.

![Shopify Download Theme file](/images/shopify-download-theme.png)
{: .centered }

I decided to go with a `theme` subfolder that contains all of the files, allowing me to have a root folder with any non-theme related files I might need to include with the project.

## Step 2: Create Release; Copy Files

I started by creating a new release pipeline, using the "Empty job" template, and naming the initial stage as "Deploy to Shopify".

![Azure DevOps - Create new pipeline](/images/azure-devops-new-pipeline.png)

Next step is to add artifacts and a trigger. I have a lot of ideas for features for this project, but at this point I am trying to just get an initial proof-of-concept. The source is the local repo, defaulting to the `master` branch. I have enabled the `Continuous deployment trigger` on the `master` branch.

![Azure DevOps - Artifacts and New Trigger](/images/azure-devops-artifacts-trigger.png)

Azure Pipelines allows for sharing variables across Pipelines and Releases in the `Library` section using [Variable groups](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/variable-groups?view=azure-devops&tabs=yaml). Taking the values from the Shopify Private app that I created, I stored the API Key, Password, and store name in new Variable group. To help prevent leaking credentials, I also marked the first two as secrets.

![Azure DevOps - Variable Group](/images/azure-devops-shopify-variables.png)



**Footnote:** Before I get mocked for publicly displaying the API key, password, and other sensitive information, please know that this was for a sandbox environment and most of it has been torn down since then. None of the credentials in this post work anymore.