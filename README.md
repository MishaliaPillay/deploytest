# deploytest

name: CI/CD Pipeline

on:
  push:
    branches:
      - main  # Runs when pushing to main branch
  pull_request:
    branches:
      - main  # Runs on pull requests to main branch

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 18
          cache: 'npm' # Cache dependencies

      - name: Install Dependencies
        run: npm install

      - name: Run Linting
        run: npm run lint

      - name: Run TypeScript Check
        run: npm run type-check  # Ensures TypeScript is error-free

      - name: Run Tests
        run: npm test

      - name: Build Project
        run: npm run build

  deploy:
    needs: build
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 18
          cache: 'npm'

      - name: Install Dependencies
        run: npm install

      - name: Build Project
        run: npm run build

      - name: Deploy to Vercel
        run: npx vercel --prod
        env:
          VERCEL_TOKEN: ${{ secrets.VERCEL_TOKEN }}
