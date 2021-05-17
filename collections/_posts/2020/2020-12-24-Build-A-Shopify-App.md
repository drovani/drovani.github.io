---
layout: post
title: Overview
series: Building a Shopify App
category: "Rovani in C&sharp;"
excerpt_separator: <!--more-->
tags:
- shopify
- react
- azure-storage
- azure-functions
---

Shopify has a really good tutorial on [building a Shopify App](https://shopify.dev/tutorials/build-a-shopify-app-with-node-and-react) using Node.js and React. It includes the initial ```node``` and ```npm``` set-up. The tutorial uses ```ngrok``` to handle allowing communication from Shopify to the local ```node``` server. Then it gives a quick GraphQL primer, uses ```Apollo``` to fetch data, goes through setting up Billing the Shopify store, and listening for webhooks. There is a pretty sizable leap from taking this information and getting a proof-of-concept app running.

This series takes a different route. I am going to start by creating a webhook listener in Azure Functions, published from Github; then create the code necessary to install the app; then create the code to allow updating the app's permissions; then get into billing for the app; and finally, actually start building some real functionality.

The goal of this is to create a solid starting point, thus lowering the barrier for entry for building future apps.

<!--more-->

{% include series.html %}

The end result of the application will be a way to manage webhooks. The default UI that Shopify provides is extremely lacking and there are a handful of features that I wish I had while working on other applications. The approach I am looking for includes:

1. Using GitHub Actions, deploy a webhook listener to Azure Functions which simply validates the webhook origin and writes it to a database.
1. Create another webhook listener which handles the Shopify App install process, uninstall, and refreshing of permissions & tokens.
1. Create a UI and an API to update billing levels/information.
1. Create an API to add, delete, pause, and update webhooks for Shopify.
1. Create a UI to view the logs and make updates.

## Future Decisions

There are several items that I need to decide on before I consider this tutorial complete. I will be updating the list (adding or striking) as I work through creating the tutorial.

- Will Azure Functions be the right product, or Azure App Services, or something else? Initial thoughts were to use Azure Functions for all API work and Azure Static Website for all UI content.
- Persistent Storage: Azure CosmosDB? Azure SQL Database? Azure Table Storage? Some combination?
- Front End Framework: React or Vue? It would be nice to use the Shopify React components but I also really like the approach Vue takes and would like to learn more.