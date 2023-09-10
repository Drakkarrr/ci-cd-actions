# Forecast potential build runner usage

In this lab you will use the `forecast` command to forecast potential GitHub Actions usage by computing metrics from completed pipeline runs in Travis CI.

## Prerequisites

1. Followed the steps [here](./readme.md#configure-your-codespace) to set up your GitHub Codespaces environment.
2. Completed the [configure lab](./1-configure.md#configuring-credentials).

## Perform a forecast

Answer the following questions before running the `forecast` command:

1. What is the date you want to start forecasting from?
    - **2022-09-02**. This date is needed as it is prior to when the bulk of builds were triggered in the demo Travis CI organization for these labs. This value defaults to the date one week ago, however, you should use a start date that will show a representative view of typical usage.
2. Where do you want to store the results?
    - **tmp/forecast**

### Steps

1. Navigate to your codespace terminal
2. Run the following command from the root directory:

    ```bash
    gh actions-importer forecast travis-ci --output-dir tmp/forecast --start-date 2022-09-02
    ```

3. The command will list all the files written to disk when the command succeeds.

    ```console
    $ gh actions-importer forecast travis-ci --output-dir tmp/forecast --start-date 2022-09-02
    [2022-08-20 22:08:20] Logs: 'tmp/forecast/log/actions-importer-20220916-021004.log'
    [2022-08-20 22:08:20] Forecasting 'http://travis-ci.com/actions-importer-labs'
    [2022-08-20 22:08:20] Output file(s):
    [2022-08-20 22:08:20]   tmp/forecast/jobs/09-16-2022-02-10_jobs_0.json
    [2022-08-20 22:08:20]   tmp/forecast/forecast_report.md
    ```

## Review the forecast report

The forecast report, logs, and completed job data will be located within the `tmp/forecast` folder.

1. Find the `forecast_report.md` file in the file explorer.
2. Right-click the `forecast_report.md` file and select `Open Preview`.
3. This file contains metrics used to forecast potential GitHub Actions usage.

### Total

The "Total" section of the forecast report contains high level statistics related to all the jobs completed after the `--start-date` CLI option:

```md
- Job count: **18**
- Pipeline count: **13**

- Execution time

  - Total: **46 minutes**
  - Median: **1 minutes**
  - P90: **6 minutes**
  - Min: **0 minutes**
  - Max: **10 minutes**

- Queue time

  - Median: **0 minutes**
  - P90: **0 minutes**
  - Min: **0 minutes**
  - Max: **0 minutes**

- Concurrent jobs

  - Median: **0**
  - P90: **0**
  - Min: **0**
  - Max: **4**
```

Here are some key terms of items defined in the forecast report:

- The `job count` is the total number of completed jobs.
- The `pipeline count` is the number of unique pipelines used.
- `Execution time` describes the amount of time a runner spent on a job. This metric can be used to help plan for the cost of GitHub-hosted runners.
  - This metric is correlated to how much you should expect to spend in GitHub Actions. This will vary depending on the hardware used for these minutes. You can use the [Actions pricing calculator](https://github.com/pricing/calculator) to estimate a dollar amount.
- `Queue time` metrics describe the amount of time a job spent waiting for a runner to be available to execute it.
- `Concurrent jobs` metrics describe the amount of jobs running at any given time. This metric can be used to define the number of runners a customer should configure.

Additionally, these metrics are defined for each queue of runners defined in Travis CI. This is especially useful if there are a mix of hosted/self-hosted runners or high/low spec machines to see metrics specific to different types of runners.

## Forecasting multiple providers

You can examine the available options for the `forecast` command by running `gh actions-importer forecast --help`. When you do this you will see the `--source-file-path` option:

```console
$ gh actions-importer forecast travis-ci -h
Options:
  -g, --travis-ci-organization <travis-ci-organization>  The Travis CI organization name.
  -u, --travis-ci-instance-url <travis-ci-instance-url>  The URL of the Travis CI instance.
  -t, --travis-ci-access-token <travis-ci-access-token>  Access token for the Travis CI instance.
  --source-file-path <source-file-path>                  The file path(s) to existing jobs data.
  -o, --output-dir <output-dir> (REQUIRED)               The location for any output files.
  --start-date <start-date>                              The start date of the forecast analysis in YYYY-MM-DD format. [default:
                                                         10/31/2022 11:13:24 AM]
  --time-slice <time-slice>                              The time slice in seconds to use for computing concurrency metrics. [default:
                                                         60]
  --credentials-file <credentials-file>                  The file containing the credentials to use.
  --no-telemetry                                         Boolean value to disallow telemetry.
  --no-ssl-verify                                        Disable ssl certificate verification.
  --no-http-cache                                        Disable caching of http responses.
  -?, -h, --help                                         Show help and usage information
```

You can use the `--source-file-path` CLI option to combine data from multiple reports into a single report. This becomes useful if you use multiple CI/CD providers and want to get a holistic view of the runner usage. This works by using the `.json` files generated by `forecast` commands as space-delimited values for the `--source-file-path` CLI option. Optionally, this value could be a glob pattern to dynamically specify the list of files (e.g. `**/*.json`).

Run the following command from within the codespace terminal:

```bash
gh actions-importer forecast --source-file-path tmp/**/jobs/*.json -o tmp/forecast-combined
```

You can now inspect the output of the command to see a forecast report using all of the files matching the `tmp/**/jobs/*.json` pattern.

### Next Lab

[Perform a dry-run of a Travis CI pipeline](4-dry-run.md)
