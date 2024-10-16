## GitHub Action for launching a Microcks test

This is a GitHub Action you may use in your Workflow to launch a Microcks test on a deployed API endpoint. Microcks tests allow you to validate an API endpoint against its OpenAPI specification, AsyncAPI specification or Postman collection definition. If test succeeds (ie. API endpoint is compliant with API contract) the workflow is pursuing, if not it fails. This action is basically a wrapper around the [Microcks CLI](https://github.com/microcks/microcks-cli) and provides the same configuration capabilities.

[![License](https://img.shields.io/github/license/microcks/microcks-cli?style=for-the-badge&logo=apache)](https://www.apache.org/licenses/LICENSE-2.0)
[![Project Chat](https://img.shields.io/badge/discord-microcks-pink.svg?color=7289da&style=for-the-badge&logo=discord)](https://microcks.io/discord-invite/)
[![Artifact HUB](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/microcks-cli-image&style=for-the-badge)](https://artifacthub.io/packages/search?repo=microcks-cli-image)
[![CNCF Landscape](https://img.shields.io/badge/CNCF%20Landscape-5699C6?style=for-the-badge&logo=cncf)](https://landscape.cncf.io/?item=app-definition-and-development--application-definition-image-build--microcks)

### Build Status

#### Fossa license and security scans

[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fmicrocks%2Ftest-github-action.svg?type=shield&issueType=license)](https://app.fossa.com/projects/git%2Bgithub.com%2Fmicrocks%2Ftest-github-action?ref=badge_shield&issueType=license)
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fmicrocks%2Ftest-github-action.svg?type=shield&issueType=security)](https://app.fossa.com/projects/git%2Bgithub.com%2Fmicrocks%2Ftest-github-action?ref=badge_shield&issueType=security)
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fmicrocks%2Ftest-github-action.svg?type=small)](https://app.fossa.com/projects/git%2Bgithub.com%2Fmicrocks%2Ftest-github-action?ref=badge_small)

#### OpenSSF best practices on Microcks core

[![CII Best Practices](https://bestpractices.coreinfrastructure.org/projects/7513/badge)](https://bestpractices.coreinfrastructure.org/projects/7513)
[![OpenSSF Scorecard](https://api.securityscorecards.dev/projects/github.com/microcks/microcks/badge)](https://securityscorecards.dev/viewer/?uri=github.com/microcks/microcks)

## Community

* [Documentation](https://microcks.io/documentation/tutorials/getting-started/)
* [Microcks Community](https://github.com/microcks/community) and community meeting
* Join us on [Discord](https://microcks.io/discord-invite/), on [GitHub Discussions](https://github.com/orgs/microcks/discussions) or [CNCF Slack #microcks channel](https://cloud-native.slack.com/archives/C05BYHW1TNJ)

To get involved with our community, please make sure you are familiar with the project's [Code of Conduct](./CODE_OF_CONDUCT.md).

## What it needs?

The `test` action needs 3 arguments:

* `<apiName:apiVersion>` : Service to test reference. Exemple: `'Beer Catalog API:0.9'`
* `<testEndpoint>` : URL where is deployed implementation to test
* `<runner>` : Test strategy (one of: `HTTP`, `SOAP`, `SOAP_UI`, `POSTMAN`, `OPEN_API_SCHEMA`, `ASYNC_API_SCHEMA`, `GRPC_PROTOBUF`, `GRAPHQL_SCHEMA`)

With a bunch of mandatory flags:

* `--microcksURL` for the Microcks API endpoint,
* `--keycloakClientId` for the Keycloak Realm Service Account ClientId,
* `--keycloakClientSecret` for the Keycloak Realm Service Account ClientSecret.

And some optional ones:

* `--waitFor` for the time to wait for test to finish (int + one of: milli, sec, min). Default is `5sec`,
* `--secretName='<Secret Name>'` is an optional flag specifying the name of a Secret to use for connecting endpoint,
* `--filteredOperations=<JSON>` allows to filter a list of operations to launch a test for,
* `--operationsHeaders=<JSON>` allows to override some operations headers for the tests to launch.

## How to use it?

Obviously we can find this action with [GitHub Actions Marketplace](https://github.com/marketplace?type=actions) :wink:

You may add the Action to your Workflow directly from the GitHub UI.

![marketplace](./assets/marketplace.png)

### Step 1 - Configure the GitHub action

```yaml
name: my-workflow
on: [push]
jobs:
  my-job:
    runs-on: ubuntu-latest
    environment: Development
    steps:
      - uses: microcks/test-github-action@v1
        with:
          apiNameAndVersion: 'API Pastry - 2.0:2.0.0'
          testEndpoint: 'http://my-api-pastry.apps.cluster.example.com'
          runner: OPEN_API_SCHEMA
          microcksURL: 'https://microcks.apps.acme.com/api/'
          keycloakClientId:  ${{ secrets.MICROCKS_SERVICE_ACCOUNT }}
          keycloakClientSecret:  ${{ secrets.MICROCKS_SERVICE_ACCOUNT_CREDENTIALS }}
          waitFor: '10sec'
```

### Step 2 - Configure the Secrets

As you probably saw just above, we do think it's a best practice to use GitHub Secrets (general or tied to `Environment` like in the example) to hold the Keycloak credentials (client Id and Secret). See below the Secrets configuration we've used for the example:

![secret configuration](./assets/secrets.png)
