# Agents

This project is LLPhant, which is a PHP library for interacting with LLMs and vector databases.

## Documentation

Besides the [README](README.md) you can find the documentation in the [docs](docs) folder.
This documentation is published on [https://llphant.readthedocs.org](https://llphant.readthedocs.org).
To run the documentation locally, follow the instructions in [Documentation.md](Documentation.md).

## Development environment

The development environment is based on [docker-compose](docker/docker-compose.yml). Assume that no PHP interpreter is
installed on the host machine. Look at the [docker README](docker/README.md) for more information. More info on
development
environment can be found in the [CONTRIBUTING](CONTRIBUTING.md)  file.

### CI pipeline

The CI pipeline is based on GitHub Actions defined in [.github/workflows](.github/workflows).
File [formats](.github/workflows/formats.yml) defines steps for checking code formatting, while
file [tests](.github/workflows/tests.yml) defines steps for running tests.

### Sources

Sources are in the [src](src) folder. There are some examples in the [examples](examples) folder, but you will ignore
them unless explicitly stated.

### Tests

Tests are written with Pest. There are two types of tests: unit tests, and integration tests. CI pipelines run only unit
tests, while integration tests are used for local development.

After having changed your current directory to [docker](docker), you can run tests from the host machine with:

```shell
docker compose exec php vendor/bin/pest tests/subfolder/TestName.php
```

For example: 

```shell
docker compose exec php vendor/bin/pest tests/Integration/Embeddings/VectorStores/OpenSearch/OpenSearchVectorStoreTest.php
```

#### Unit tests

Unit tests are in the [tests](tests/Unit) folder. To run unit tests use `composer test:unit` inside the `php`
container.

#### Integration tests

Integration tests are in the [integration](tests/Integration) folder. To run integration tests you have to set up the
environment variables in the [docker/.env](docker/.env) file.

## Base44 sandbox environment

This is a PHP **library**, not a web application — there is no web UI. The Base44 sandbox
(`docker-compose.base44.yml`) brings up a single PHP 8.2 container that:

- Bind-mounts the repo source at `/var/llphant` (edits are live).
- Runs `composer install --no-interaction --prefer-dist` on startup.
- Serves a development status page on port 3000 via PHP's built-in server.
- Runs as `root` (the sandbox host directory is `drwx------ root`, so the
  Dockerfile's `llphant` user cannot traverse the mount).

To verify the environment:

```shell
docker compose -f docker-compose.base44.yml up -d          # start
curl -sf http://localhost:3000/                            # status page
docker compose -f docker-compose.base44.yml exec php composer test:unit   # 237 tests, all pass
```

No external credentials are needed for the sandbox or unit tests. Integration tests
require API keys (OpenAI, Anthropic, etc.) and database services — see `docker/.env.dist`.
