# Perform a dry run of a Travis CI pipeline

In this lab you will use the `dry-run` command to convert a Travis CI pipeline to its equivalent GitHub Actions workflow.

## Prerequisites

1. Followed the steps [here](./readme.md#configure-your-codespace) to set up your codespace environment.
2. Completed the [configure lab](./1-configure.md#configuring-credentials).
3. Completed the [audit lab](./2-audit.md).

## Perform a dry run

You will be performing a dry run against a Travis CI project. Answer the following questions before running this command:

1. What pipeline do you want to convert?
    - __travisci-ruby-example__.  This is one of the sample projects available in the `actions-importer-labs` organization.

2. Where do you want to store the result?
    - __tmp/dry-run__. This can be any path within the working directory that GitHub Actions Importer commands are executed from.

### Steps

1. Navigate to your codespace terminal.
2. Run the following command from the root directory:

    ```bash
    gh actions-importer dry-run travis-ci --travis-ci-repository "travisci-ruby-example" --output-dir tmp/dry-run
    ```

3. The command will list all the files written to disk when the command succeeds.

    ```console
    $ gh actions-importer dry-run travis-ci --travis-ci-repository "travisci-ruby-example" --output-dir tmp/dry-run
    [2022-09-19 19:46:03] Logs: 'tmp/dry-run/log/actions-importer-20220919-194603.log'     
    [2022-09-19 19:46:05] Output file(s):                                           
    [2022-09-19 19:46:05]   tmp/dry-run/actions-importer-labs/travisci-ruby-example/.github/workflows/travisci-ruby-example.yml
    ```

4. View the converted workflow:
    - Find `tmp/dry-run/actions-importer-labs/travisci-ruby-example/.github/workflows` in the file explorer pane in your codespace.
    - Click `travisci-ruby-example.yml` to open.

## Inspect the output files

The files generated from the `dry-run` command represent the equivalent Actions workflow for the Travis CI project. The Travis CI configuration and converted workflow can be seen below:

<details>
  <summary><em>Travis CI configuration 👇</em></summary>

```yaml
  language: ruby
  sudo: false
  dist: trusty
  rvm:
  - 1.9.3
  - 2.0.0
  - 2.1.0

  install: 
  - gem install bundler

  script:
  - echo "hello!"

  jobs: 
    include: 
      - script: echo "child"
```

</details>

<details>
  <summary><em>Converted workflow 👇</em></summary>

```yaml
name: actions-importer-labs/travisci-ruby-example
on:
  push:
    branches:
    - "**/*"
  pull_request:
concurrency:
#   # This item has no matching transformer
#   maximum_number_of_builds: 0
jobs:
  test:
    runs-on: # this agent type is not supported: [[{"dist"=>"trusty"}]]
             ubuntu-latest
    steps:
    - name: checkout
      uses: actions/checkout@v2
    - uses: ruby/setup-ruby@v1
      with:
        ruby-version: "${{ matrix.rvm }}"
#     # 'sudo' was not transformed because there is no suitable equivalent in GitHub Actions
    - run: gem install bundler
    - run: echo "hello!"
    strategy:
      matrix:
        rvm:
        - 1.9.3
        - 2.0.0
        - 2.1.0
  test_2:
    runs-on: # this agent type is not supported: [[{"dist"=>"trusty"}]]
             ubuntu-latest
    steps:
    - name: checkout
      uses: actions/checkout@v2
    - uses: ruby/setup-ruby@v1
      with:
        ruby-version: 1.9.3
#     # 'sudo' was not transformed because there is no suitable equivalent in GitHub Actions
    - run: gem install bundler
    - run: echo "child"
```

</details>

Despite these two pipelines using different syntax they will function equivalently.

## Next lab

[Use custom transformers to customize GitHub Actions Importer's behavior](./5-custom-transformers.md)
