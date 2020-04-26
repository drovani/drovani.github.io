---
layout: guide
guide: build_a_shopify_app
title: Build A Shopify App Tutorial
category: "Rovani in C&sharp;"
last_updated: 2020-04-25
tags:
- shopify
- node
- react
- azure-storage
- azure-functions
---

Shopify has a really good tutorial on [building a Shopify App](https://shopify.dev/tutorials/build-a-shopify-app-with-node-and-react) using Node.js and React. It includes the initial ```node``` and ```npm``` set-up. The tutorial uses ```ngrok``` to handle allowing communication from Shopify to the local ```node``` server. Then it gives a quick GraphQL primer, uses ```Apollo``` to fetch data, goes through setting up Billing the Shopify store, and listening for webhooks. There is a pretty sizable leap from taking this information and getting a proof-of-concept app running. This series of steps adapts the tutorial from Shopify and add some more steps to get an app deployed on Azure.
