# Meilisearch Destination

This is the repository for the Meilisearch destination connector, written in Python.
For information about how to use this connector within Airbyte, see [the documentation](https://docs.airbyte.io/integrations/destinations/meilisearch).

## Local development

### Prerequisites

**To iterate on this connector, make sure to complete this prerequisites section.**

#### Minimum Python version required `= 3.9.0`

#### Build & Activate Virtual Environment and install dependencies

This project is using Poetry for dependency management and build system:

```bash
poetry install --with dev
```

#### Create credentials

**If you are a community contributor**, follow the instructions in the [documentation](https://docs.airbyte.io/integrations/destinations/meilisearch)
to generate the necessary credentials. Then create a file `secrets/config.json` conforming to the `destination_meilisearch/spec.json` file.
Note that the `secrets` directory is gitignored by default, so there is no danger of accidentally checking in sensitive information.
See `integration_tests/sample_config.json` for a sample config file.

**If you are an Airbyte core member**, copy the credentials in Lastpass under the secret name `destination meilisearch test creds`
and place them into `secrets/config.json`.

### Locally running the connector

```
python main.py spec
python main.py check --config secrets/config.json
python main.py discover --config secrets/config.json
python main.py read --config secrets/config.json --catalog integration_tests/configured_catalog.json
```

### Locally running the connector docker image

#### Build

**Via [`airbyte-ci`](https://github.com/airbytehq/airbyte/blob/master/airbyte-ci/connectors/pipelines/README.md) (recommended):**

```bash
airbyte-ci connectors --name=destination-meilisearch build
```

An image will be built with the tag `airbyte/destination-meilisearch:dev`.

**Via `docker build`:**

```bash
docker build -t airbyte/destination-meilisearch:dev .
```

#### Run

Then run any of the connector commands as follows:

```
docker run --rm airbyte/destination-meilisearch:dev spec
docker run --rm -v $(pwd)/secrets:/secrets airbyte/destination-meilisearch:dev check --config /secrets/config.json
# messages.jsonl is a file containing line-separated JSON representing AirbyteMessages
cat messages.jsonl | docker run --rm -v $(pwd)/secrets:/secrets -v $(pwd)/integration_tests:/integration_tests airbyte/destination-meilisearch:dev write --config /secrets/config.json --catalog /integration_tests/configured_catalog.json
```

## Testing

You can run our full test suite locally using [`airbyte-ci`](https://github.com/airbytehq/airbyte/blob/master/airbyte-ci/connectors/pipelines/README.md):

```bash
airbyte-ci connectors --name=destination-meilisearch test
```

### Customizing acceptance Tests

Customize `acceptance-test-config.yml` file to configure tests. See [Connector Acceptance Tests](https://docs.airbyte.com/connector-development/testing-connectors/connector-acceptance-tests-reference) for more information.
If your connector requires to create or destroy resources for use during acceptance tests create fixtures for it and place them inside integration_tests/acceptance.py.

## Dependency Management

All of your dependencies should go in `setup.py`, NOT `requirements.txt`. The requirements file is only used to connect internal Airbyte dependencies in the monorepo for local development.
We split dependencies between two groups, dependencies that are:

- required for your connector to work need to go to `MAIN_REQUIREMENTS` list.
- required for the testing need to go to `TEST_REQUIREMENTS` list

### Publishing a new version of the connector

You've checked out the repo, implemented a million dollar feature, and you're ready to share your changes with the world. Now what?

1. Make sure your changes are passing our test suite: `airbyte-ci connectors --name=destination-meilisearch test`
2. Bump the connector version in `metadata.yaml`: increment the `dockerImageTag` value. Please follow [semantic versioning for connectors](https://docs.airbyte.com/contributing-to-airbyte/resources/pull-requests-handbook/#semantic-versioning-for-connectors).
3. Make sure the `metadata.yaml` content is up to date.
4. Make the connector documentation and its changelog is up to date (`docs/integrations/destinations/meilisearch.md`).
5. Create a Pull Request: use [our PR naming conventions](https://docs.airbyte.com/contributing-to-airbyte/resources/pull-requests-handbook/#pull-request-title-convention).
6. Pat yourself on the back for being an awesome contributor.
7. Someone from Airbyte will take a look at your PR and iterate with you to merge it into master.
