# Doc Detective as a GitHub Action

:octocat: The official GitHub Action for [Doc Detective](https://github.com/doc-detective/doc-detective). Keep your docs accurate with ease.

> ⚠️ This action is still in development.
> - It's only been tested on Ubuntu.
> - It only works with an in-development version of Doc Detective. The `version` input must be set to `dev`.
> - It only supports Firefox and Chrome contexts with `headless` set to `true`. It's incompatible with Safari, Edge, and Doc Detective's `startRecording` action.

## Usage

Create a YAML file in your `.github/workflows` directory with the following content:

```yaml
name: doc-detective
on: [pull_request]

jobs:
  runTests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: doc-detective/doc-detective-action
```

## File structure

This action runs in the current working directory of the workflow. If you want to change the directory, you can do so by adding a `working-directory` key to the `doc-detective-action` step:

```yaml
- uses: doc-detective/doc-detective-action
  working-directory: path/to/your/directory
```

Just like the main project, this action looks for a `.doc-detective.json` file in the current directory for the configuration if it isn't specified in the `config` input.

All paths are relative to the current working directory.

## Inputs

You can customize the action with the following optional inputs:

To add an input, edit your workflow file to include the `with` key to the `uses` block. For example:

```yaml
- uses: doc-detective/doc-detective-action
  with:
    version: latest
```

### `version` (default: latest)

Specify the version of Doc Detective to use. This can be a specific version number or an NPM tag (like `latest`).

```yaml
- uses: doc-detective/doc-detective-action
  with:
    version: 2.15.0
```

### `command` (default: `runTests`)

The command to run. Valid values are "runTests" and "runCoverage".

```yaml
- uses: doc-detective/doc-detective-action
  with:
    command: runCoverage
```

### `config` (default: `.doc-detective.json`)

The path to the configuration file.

```yaml
- uses: doc-detective/doc-detective-action
  with:
    config: path/to/your/config.json
```

### `input` (default: `.`)

Path to the input file or directory. Overrides the "input" field in the config file.

```yaml
- uses: doc-detective/doc-detective-action
  with:
    input: path/to/your/tests
```

### `exitOnFail` (default: `false`)

Exit with a non-zero code if one or more tests fails. Only valid if `command` is "runTests".

```yaml
- uses: doc-detective/doc-detective-action
  with:
    exitOnFail: true
```

### `createIssueOnFail` (default: `false`)

Create a GitHub issue if one or more tests fails. Only valid if `command` is "runTests".

```yaml
- uses: doc-detective/doc-detective-action
  with:
    createIssueOnFail: true
```

This input requires the workflow or job to have `write` access for the `issues` scope. You can set the necessary permissions in the workflow file like this:

```yaml
name: doc-detective
on: [pull_request]

jobs:
  runTests:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      issues: write
    steps:
      - uses: actions/checkout@v4
      - uses: doc-detective/doc-detective-action
        with:
          createIssueOnFail: true
```

### `token` (default: `${{ github.token }}`)

The GitHub token to use for creating issues. Defaults to the token already available to the GitHub Action workflow. Only set this if you want to override the default token.

```yaml
- uses: doc-detective/doc-detective-action
  with:
    createIssueOnFail: true
    token: ${{ secrets.MY_GITHUB_TOKEN }}
```

## Outputs

The action sets the following outputs:

### `results`

JSON-formatted results of the command. This can be used in subsequent steps in the same job.

```yaml
- uses: doc-detective/doc-detective-action
  id: doc-detective
- run: echo "${{ steps.doc-detective.outputs.results }}"
```
