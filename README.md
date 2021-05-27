# stoplight-docs-uitpas

This is the repository behind https://publiq.stoplight.io/docs/uitpas

## Contribution

1. Create a branch with your changes
2. Create a PR for your branch
3. Fix any problems detected by the automatic checks
4. Merge once someone has reviewed and approved your changes

Be sure to also check the [internal guidelines](https://publiq.stoplight.io/docs/guidelines) on how to design APIs and write documentation. **(Login required)**

## Automatic checks

The automatic checks will be run via GitHub actions for every commit pushed to every branch.

Some examples of automatic checks are dead links in the docs, malformed Markdown in the docs, or invalid JSON in the OpenAPI file(s).

To run them locally, you'll need [node](https://nodejs.org/en/) and [yarn](https://yarnpkg.com/getting-started/install).

### Installing the required packages

To install or update the required packages to run the checks, run `yarn install`.

### Checking the Markdown files for errors

Run `yarn docs:lint` to check for errors or warnings. If any errors or warnings are detected, you can either fix them manually or try to fix them with `yarn docs:lint:fix`

### Checking the OpenAPI files for errors

Run `yarn openapi:lint` to check for errors or warnings. If any errors or warnings are detected, you need to fix them manually for now.
