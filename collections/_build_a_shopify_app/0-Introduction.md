---
title: Introduction to Node.js and React and Azure
---

### Requirements

Before you begin, make sure you have the background knowledge and skills to complete this tutorial:

- You should be comfortable using Visual Studio Code, WSL, Git.
- You'll need to be able to read and write HTML, CSS, and Javascript.
- You should be familiar with installing software using the npm package manager.
- If you don't already have one, you'll need to [create a Shopify Partner account](https://partners.shopify.com/signup) and [create an Azure account](https://azure.microsoft.com/en-us/free/).

### Tools
You’ll use several frameworks, tools, and libraries to build your app. You should need only a general knowledge and understanding of them to complete this tutorial. We use many of these tools at Shopify to build our own apps, and we believe this technology stack will help you get up and running fast.

#### Node.js
[Node.js](https://nodejs.org/en/) is an open-source, cross-platform JavaScript run-time environment. You’ll use it to create a server to host your app.

#### React
[React](https://reactjs.org/) is a JavaScript library for building component-based user interfaces. Building with components makes it faster for you to build and makes interfaces more consistent for users. Shopify uses React for its flexibility and performance.

#### Next.js
[Next.js](https://nextjs.org/) is a framework for quickly setting up React-based apps. It provides a helpful baseline configuration, so you don’t have to manually set up features like URL routing or server-side rendering.

#### GraphQL
[GraphQL](https://graphql.org/) is a query language for interacting with an API. It streamlines the way your app talks to Shopify’s platform, and provides only the data you ask for, reducing bandwidth and overhead. Most of Shopify already runs on GraphQL and we believe the learning curve will be worth the investment for developers.

#### Apollo
[Apollo](https://www.apollographql.com/) is a family of JavaScript libraries that makes it easier to work with GraphQL. You’ll use Apollo Client to create a React component that conveniently handles all your operations with Shopify’s API.

#### Polaris
[Polaris](https://polaris.shopify.com/) is Shopify’s React component library and design system. It includes guidelines for design, content, and accessibility. It also includes a full-featured library of ready-to-use React components. Using Polaris components helps your app look and feel native to Shopify, so it’s easier to use for merchants.