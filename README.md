# GitHub-Actions-Sample
GitHub-Actions-Sample React.js


### 今回作成したサンプル用の CI/CDファイル

```yml
name: Sample-Workflow

on: [push]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  setup:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - uses: actions/cache@v3
        id: npm-cache
        with:
          path: "**/node_modules"
          key: ${{ runner.os }}-${{ hashFiles('**/package-lock.json') }}-{{ checksum "patches.hash" }}
      - name: Install packages
        run: cd frontend && npm ci
  frontend-test:
    runs-on: ubuntu-latest
    needs: [setup]
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - uses: actions/cache@v3
        with:
          path: "**/node_modules"
          key: ${{ runner.os }}-${{ hashFiles('**/package-lock.json') }}
      - name: Frontend test
        run: cd frontend && npm run test
        env:
          CI: true
  frontend-build:
    runs-on: ubuntu-latest
    needs: [frontend-test]
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - uses: actions/cache@v3
        with:
          path: "**/node_modules"
          key: ${{ runner.os }}-${{ hashFiles('**/package-lock.json') }}
      - name: Frontend build
        run: cd frontend && npm run build
        env:
          CI: false
          PUBLIC_URL: /GitHub-Actions-Sample
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          path: './frontend/build'
  deploy:
    runs-on: ubuntu-latest
    needs: [frontend-build]
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```
