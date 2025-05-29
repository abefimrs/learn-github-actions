# Github Actions Hands-On Lab

## Create a Simple Workflow

Task: Students create a workflow that runs on push and prints a message.

Steps:
  1. Create yml files in their repo in folder .github/workflows/.
  2. Copy the actions code into file
  3. Push to GitHub and show live results in the Actions tab.

**Practical 1 : Hello World Workflow**

File: .github/workflows/hello-world.yml
```
name: Hello World Workflow

on:
  push:
    branches: [ "main" ]

jobs:
  greet:
    runs-on: ubuntu-latest
    steps:
      - name: Say Hello
        run: echo " Hello, GitHub Actions!"
```

Steps for check:
Create this file in their repository.
Push to GitHub.
Check the Actions tab to see the workflow run and logs.

## Deep Dive into Key Features

**Practical 2 :  Jobs with Dependencies**

File: .github/workflows/dependent-jobs.yml
```
name: Dependent Jobs

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Building the app..."

  test:
    runs-on: ubuntu-latest
    needs: build  # Runs only after "build" job
    steps:
      - run: echo " Running tests..."

  deploy:
    runs-on: ubuntu-latest
    needs: [build, test]
    steps:
      - run: echo "Deploying to production..."
```
## Actions Marketplace
**Practical 3 :  Using Marketplace Actions (Node.js Setup)**

File: .github/workflows/nodejs-ci.yml

```
name: Node.js CI

on: [pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4  # Checkout code
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm install
      - run: npm test
```
**Practical 4 :  Secrets & Environment Variables**
```
steps:
  - name: Use a Secret
    env:
      API_KEY: ${{ secrets.MY_API_KEY }}
    run: echo "API key is $API_KEY"
```

**Practical 5 : Matrix Strategy (Test Across Versions)**

File: .github/workflows/matrix.yml
```
name: Matrix Test
on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18, 20, 21]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      - run: node --version
```
**Practical 6 : Caching Dependencies**
```
steps:
  - uses: actions/checkout@v4
  - uses: actions/cache@v3
    with:
      path: node_modules
      key: ${{ runner.os }}-node-${{ hashFiles('package-lock.json') }}
  - run: npm install
```
## Build a CI/CD Pipeline 
Task: Automate testing and deployment for a sample app (e.g., Node.js).
steps:
  1. Use actions/checkout to fetch code.
  2. Set up Node.js with actions/setup-node.
  3. Run npm install && npm test.

Add a deployment step (e.g., deploy to GitHub Pages or AWS S3).

**Practical 7 :  Deploy to GitHub Pages**

File: .github/workflows/deploy.yml

```
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm install
      - run: npm test

  deploy:
    runs-on: ubuntu-latest
    needs: test  # Only deploy if tests pass
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm install
      - run: npm run build
      - uses: peaceiris/actions-gh-pages@v3  # Deploy to GH Pages
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

## Real-World Use Cases & Best Practices

Examples:
Auto-deploy a static site to GitHub Pages.
Run security scans with CodeQL.
Notify Slack on failure.

**Practical 8 :  Security Scanning with CodeQL**
```
name: CodeQL Analysis

on:
  schedule:
    - cron: '0 0 * * 0'  # Weekly scan
  push:
    branches: [main]

jobs:
  analyze:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: github/codeql-action/init@v2
        with:
          languages: javascript
      - uses: github/codeql-action/analyze@v2
```
