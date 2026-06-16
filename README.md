# Buddy Download Artifact GitHub Action

Download artifacts from Buddy CI/CD platform in GitHub Actions workflows.

## Features

- Download artifacts directly in GitHub Actions
- Support for versioned artifacts (artifact@version)
- Merge or replace directory contents

## Usage

### Basic Usage

```yaml
name: Download
on: [push]

jobs:
  download:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Login to Buddy
        uses: buddy/login@v1
        with:
          token: ${{ secrets.BUDDY_TOKEN }}
          region: 'US'

      - name: Download artifact
        uses: buddy/artifact-download@v1
        with:
          workspace: my-workspace
          project: my-project
          identifier: my-artifact
          directory: ./artifacts
```

### Download Specific Version

```yaml
- name: Download versioned artifact
  uses: buddy/artifact-download@v1
  with:
    workspace: my-workspace
    project: my-project
    identifier: my-artifact@1.0.0
    directory: ./artifacts
```

### Merge With Existing Directory

```yaml
- name: Download and merge
  uses: buddy/artifact-download@v1
  with:
    workspace: my-workspace
    project: my-project
    identifier: my-artifact
    directory: ./artifacts
    merge: true
```

### Replace Directory Contents

```yaml
- name: Download and replace
  uses: buddy/artifact-download@v1
  with:
    workspace: my-workspace
    project: my-project
    identifier: my-artifact
    directory: ./artifacts
    replace: true
```

## Inputs

| Input        | Required | Description                                                           |
| ------------ | -------- | --------------------------------------------------------------------- |
| `workspace`  | Yes      | Buddy workspace domain                                                |
| `project`    | Yes      | Buddy project name (URL handle)                                       |
| `identifier` | Yes      | Artifact identifier with optional version (e.g., `my-artifact@1.0.0`) |
| `directory`  | Yes      | Path to the directory to download to                                  |
| `merge`      | No       | Merge contents of the directory with artifact (`true`/`false`)        |
| `replace`    | No       | Replace contents of the directory with artifact (`true`/`false`)      |
| `debug`      | No       | Run the BDY CLI in DEBUG mode and dump `~/.bdy/cli.log` on failure    |

## Outputs

| Output          | Description                                |
| --------------- | ------------------------------------------ |
| `artifact_path` | The path where the artifact was downloaded |

## Environment Variables

The action exports the following environment variables for use in subsequent steps:

| Variable              | Description                                |
| --------------------- | ------------------------------------------ |
| `BUDDY_ARTIFACT_PATH` | The path where the artifact was downloaded |

## Prerequisites

This action requires authentication with Buddy. Use the [`buddy/login`](https://github.com/buddy/login) action before downloading artifacts:

```yaml
- name: Login to Buddy
  uses: buddy/login@v1
  with:
    token: ${{ secrets.BUDDY_TOKEN }}
    region: 'US'
```

The login action sets the following environment variables that are used by this action:

- `BUDDY_TOKEN` - Authentication token
- `BUDDY_API_ENDPOINT` - API endpoint URL

### BDY CLI Installation

By default, this action automatically installs the latest BDY CLI from the production channel. If you need a specific version or channel, use the `buddy/setup` action first:

```yaml
- name: Setup BDY CLI
  uses: buddy/setup@v1
  with:
    version: '1.12.8'
```

See the [`buddy/setup`](https://github.com/buddy/setup) action for more options.

## Debugging

If a BDY CLI command fails, the action throws an error that includes the exit code, the command that was run, and its captured output.

Enabling debug runs the BDY CLI in DEBUG mode, so it writes verbose logs to `~/.bdy/cli.log`, and on failure dumps that log (plus a stack trace) into the workflow output. Enable it in either of these ways:

- **Per step** — set the `debug` input on this action:

  ```yaml
  - uses: buddy/artifact-download@v1
    with:
      # ...
      debug: true
  ```

- **Whole run** — enable [GitHub step debug logging](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/enabling-debug-logging), either by ticking **Enable debug logging** in the "Re-run jobs" dialog or by setting the `ACTIONS_STEP_DEBUG` repository secret/variable to `true`.

## License

MIT - See [LICENSE.md](LICENSE.md) for details.
