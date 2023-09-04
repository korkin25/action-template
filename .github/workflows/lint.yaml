---
name: Check Syntax

on:
  push:
    paths:
      - '**/*.yml'
      - '**/*.yaml'
  pull_request:
    paths:
      - '**/*.yml'
      - '**/*.yaml'
  workflow_dispatch:

env:
  ACTIONLINT_VERSION: "1.6.25"
  YAMLLINT_VERSION: "1.32.0"

jobs:
  lint:
    name: Validate Syntax
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Cache pip data
        id: cache_pip
        uses: actions/cache@v2
        with:
          path: ~/.cache/pip
          key: pip-data-${{ runner.os }}-yamllint-${{ env.YAMLLINT_VERSION }}
          restore-keys: pip-data-${{ runner.os }}-yamllint-${{ env.YAMLLINT_VERSION }}

      - name: Cache actionlint
        id: cache_actionlint
        uses: actions/cache@v2
        with:
          path: ~/.local/bin/actionlint
          key: actionlint-${{ env.ACTIONLINT_VERSION }}

      - name: Install yamllint
        if: steps.cache_pip.outputs.cache-hit != 'true'
        run: pip install yamllint==${{ env.YAMLLINT_VERSION }}

      - name: Install actionlint
        if: steps.cache_actionlint.outputs.cache-hit != 'true'
        run: |
          mkdir -p "${HOME}/.local/bin"
          curl -sSLf "https://github.com/rhysd/actionlint/releases/download/v${{ env.ACTIONLINT_VERSION }}/actionlint_${{ env.ACTIONLINT_VERSION }}_linux_amd64.tar.gz" | tar xzv -C "${HOME}/.local/bin/"

      - name: Check for yamllint config
        run: |
          if [ -f ".yamllint" ]; then
            echo "Using detected .yamllint config path."
            echo "YAMLLINT_CONFIG_PATH=.yamllint" >> "$GITHUB_ENV"
          else
            echo "Using default yamllint config path: ${{ github.workspace }}/.github/yamllint.yaml"
            echo "YAMLLINT_CONFIG_PATH=${{ github.workspace }}/.github/yamllint.yaml" >> "$GITHUB_ENV"
          fi

      - name: Check YAML Syntax for All Files
        run: |
          find . -name "*.yml" -o -name "*.yaml" | while read -r file; do
            yamllint -c "${YAMLLINT_CONFIG_PATH}" "$file"
          done

      - name: Check GitHub Actions Workflow Syntax
        run: ~/.local/bin/actionlint
