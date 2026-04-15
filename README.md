# KOREXT Enforce Action

Enforce compliance policies on AI generated code in your GitHub workflows.

72 policy packs. 532 rules. 13 languages. Violations appear as GitHub Code Scanning annotations on pull requests.

## Quick Start

Add this to `.github/workflows/korext.yml`:

```yaml
name: Korext Enforcement
on: [push, pull_request]

permissions:
  contents: read
  security-events: write

jobs:
  enforce:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: Korext/enforce-action@v3
        with:
          api-token: ${{ secrets.KOREXT_API_TOKEN }}
```

Korext scans your code on every push and PR using the default `web` policy pack.

## How It Works

1. **Install**: The action installs the [Korext CLI](https://www.npmjs.com/package/korext)
2. **Scan**: Runs `korext enforce` against your codebase with the selected policy pack
3. **Report**: Generates a SARIF file and uploads it to GitHub Code Scanning
4. **Gate**: Fails the workflow if critical or high severity violations are found

Violations appear as annotations directly on the PR diff, powered by GitHub Code Scanning.

## Inputs

| Input | Description | Required | Default |
|---|---|---|---|
| `directory` | Directory to scan for policy violations | No | `.` |
| `pack` | Policy Pack ID to enforce | No | `web` |
| `api-token` | Korext API token for authenticated mode | No | (anonymous) |
| `fail-on-violations` | Fail workflow on critical/high violations | No | `true` |
| `sarif-upload` | Upload SARIF to GitHub Code Scanning | No | `true` |
| `region` | Data processing region (us, eu, apac) | No | (default) |
| `sign-bundles` | Request signed proof bundles | No | `true` |

## Outputs

| Output | Description |
|---|---|
| `violations` | Total number of policy violations found |
| `sarif-file` | Path to the generated SARIF results file |
| `bundle-count` | Number of proof bundles generated |
| `bundles-signed` | Number of signed proof bundles |
| `bundle-ids` | Comma separated list of proof bundle IDs |

## Examples

### Multiple Policy Packs

```yaml
- uses: Korext/enforce-action@v3
  with:
    pack: web,pci-dss-v1,owasp-v1
    api-token: ${{ secrets.KOREXT_API_TOKEN }}
```

### EU Data Sovereignty

```yaml
- uses: Korext/enforce-action@v3
  with:
    pack: gdpr-v1
    region: eu
    api-token: ${{ secrets.KOREXT_API_TOKEN }}
```

### Scan Specific Directory

```yaml
- uses: Korext/enforce-action@v3
  with:
    directory: src/
    pack: hipaa-v1
    api-token: ${{ secrets.KOREXT_API_TOKEN }}
```

### Warn Only (do not fail)

```yaml
- uses: Korext/enforce-action@v3
  with:
    pack: web
    fail-on-violations: 'false'
```

## Authentication

For full access to all policy packs and signed proof bundles, create an API token in your [KOREXT dashboard](https://app.korext.com) and add it as a GitHub secret:

1. Go to [app.korext.com](https://app.korext.com) > Settings > API Tokens
2. Create a new token
3. Add it as `KOREXT_API_TOKEN` in your repo's Settings > Secrets and variables > Actions

Without a token, the action runs in anonymous mode (20 requests per hour, limited packs).

## Links

- [Website](https://korext.com)
- [Dashboard](https://app.korext.com)
- [Documentation](https://korext.com/docs)
- [CLI on npm](https://www.npmjs.com/package/korext)
- [VS Code Extension](https://marketplace.visualstudio.com/items?itemName=Korext.korext)

## License

Proprietary. See [Terms of Service](https://korext.com/legal).
