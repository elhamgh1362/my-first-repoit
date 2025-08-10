# my-first-repoit
just testing github
# --- GitHub Actions Workflow for Node.js ---
# مسیر: .github/workflows/ci.yml

name: CI

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18.x, 20.x]
    steps:
      - uses: actions/checkout@v4

      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Run tests
        run: npm test

      - name: Build
        run: npm run build

      - name: Upload test results (optional)
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-logs
          path: test-results || ./reports || ./coverage || .

# --- Shell Script for Initial Repository Setup ---
# فایل: setup-repo.sh
# استفاده: bash setup-repo.sh origin https://github.com/username/repo.git

#!/usr/bin/env bash
set -euo pipefail

REMOTE_NAME=${1:-origin}
REMOTE_URL=${2:-}

if [[ -z "$REMOTE_URL" ]]; then
  echo "Usage: $0 <remote-name> <remote-url>"
  exit 1
fi

if [ ! -d .git ]; then
  git init
  echo "Initialized empty git repository."
fi

git add .
git commit -m "Initial commit" || echo "Nothing to commit."

git remote add "$REMOTE_NAME" "$REMOTE_URL" 2>/dev/null || echo "Remote $REMOTE_NAME already exists."
git branch -M main 2>/dev/null || git branch -M master || true

git push -u "$REMOTE_NAME" main
echo "Pushed to $REMOTE_NAME/main"
