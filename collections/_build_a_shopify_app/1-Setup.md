---
title: Set up your local app instance
---

Start by setting up the tools and dependencies that you'll need to build and run your app. These commands are all done in the Visual Studio terminal using bash on [WSL](https://docs.microsoft.com/en-us/windows/wsl/about?context=/windows/dev-environment/dev-environment-context).

### Install the latest stable version of Node.js

There are many, many ways to do this. For the purposes of this tutorial, we use Ubuntu 18.04 running in WSL. All terminal commands are run in bash (from inside Visual Studio Code).

```terminal
node -v
```

If Node.js is already installed, then this will return the version on your computer. As of the writing of this tutorial, Azure Functions [only supports](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node#node-version) Node.js version ~12. Using NVM (**n**ode **v**ersion **m**anager), you can have it install the latest version of Node.js 12.

```terminal
nvm install 12
nvm alias default 12
```

This will install the latest version of Node.js 12 and set it as the default version anytime you open the terminal.

### Create a new git repo

All projects start with a new Git repo (you're starting all projects with a git repo, right?).

1. Follow the tutorial on how to [Create a new Git repo in your project](https://docs.microsoft.com/en-us/azure/devops/repos/git/create-new-repo?view=azure-devops)
1. Once created, clone the repository to VS Code.

#### Create a package.json file

Node.js includes a tool called `npm` (**n**ode **p**ackage **m**anager) that manages Node.js packages. Initializing an `npm` project creates a `package.json` file that contains all of the projects your project depends upon.

```terminal
npm init -y
```

Open `package.json` and edit any of the default descriptor properties such as name, description, author, and version.

### Build a React scaffold using Next.js

You'll use a React framework called Next.js to build you app. Next.js takes care of some of the things that you'd normally have to do when building a React app, including:

- webpack configuration
- hot module replacement
- server-side rendering
- client-side routing
- updating babel configuration
- code splitting

```terminal
npm install --save react react-dom next
```

This adds the three `npm` packages to your `package.json` file then downloads them from the `npm` repository.

### Create a view

Views are the pages that hold the frontend components that display the data your app queries and mutates. Next.js automatically routes views that are stored in a `pages` directory using the file name. This makes it easier to build your app application later in the tutorial.

```terminal
mkdir pages
touch pages/index.js
```

Open `pages/index.js` in the editor and add the following content.

```js
const Index = () => (
    <div>
        <p>Sample app using Reach and Next.js</p>
    </div>
);

export default Index;
```

### Add scripts to build and start your app

Edit `package.json` and add the `dev`, `build`, and `start` scripts to the file:

```json
{
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```

### Run your project

Just to prove that everything is working as expected, kick off a local webserver by running the `npm` script for `dev`.

```terminal
npm run dev
```

The terminal output should indicate that the development server has started and that it is now `ready on http://localhost:3000`. Ctrl+click that link (or type it into a browser) and you should see the results of your hard work.

### Commit to Azure DevOps Git repo

Now that everything is working, commit the code up to your Git repo. The next steps in this tutorial will guide you through enabling it as a 