# Querying Loki with LogCLI

If you prefer a command line interface, LogCLI also allows users to run LogQL
queries against a Loki server.

## Installation

### Binary (Recommended)

Every release includes binaries for `logcli` which can be found on the
[Releases page](https://github.com/grafana/loki/releases).

### From source

Use `go get` to install `logcli` to `$GOPATH/bin`:

```
$ go get github.com/grafana/loki/cmd/logcli
```

## Usage

### Example

If you are running on Grafana Cloud, use:

```bash
$ export LOKI_ADDR=https://logs-us-west1.grafana.net
$ export LOKI_USERNAME=<username>
$ export LOKI_PASSWORD=<password>
```

Otherwise you can point LogCLI to a local instance directly
without needing a username and password:

```bash
$ export LOKI_ADDR=http://localhost:3100
```

> Note: If you are running Loki behind a proxy server and you have
> authentication configured, you will also have to pass in LOKI_USERNAME
> and LOKI_PASSWORD accordingly.

```bash
$ logcli labels job
https://logs-dev-ops-tools1.grafana.net/api/prom/label/job/values
cortex-ops/consul
cortex-ops/cortex-gw
...

$ logcli query '{job="cortex-ops/consul"}'
https://logs-dev-ops-tools1.grafana.net/api/prom/query?query=%7Bjob%3D%22cortex-ops%2Fconsul%22%7D&limit=30&start=1529928228&end=1529931828&direction=backward&regexp=
Common labels: {job="cortex-ops/consul", namespace="cortex-ops"}
2018-06-25T12:52:09Z {instance="consul-8576459955-pl75w"} 2018/06/25 12:52:09 [INFO] raft: Snapshot to 475409 complete
2018-06-25T12:52:09Z {instance="consul-8576459955-pl75w"} 2018/06/25 12:52:09 [INFO] raft: Compacting logs from 456973 to 465169
```

### Configuration

Configuration values are considered in the following order (lowest to highest):

- Environment variables
- Command line flags

### Details

```nohighlight
$ logcli help
usage: logcli [<flags>] <command> [<args> ...]

A command-line for loki.

Flags:
      --help             Show context-sensitive help (also try --help-long and
                         --help-man).
      --version          Show application version.
  -q, --quiet            suppress query metadata
      --stats            show query statistics
  -o, --output=default   specify output mode [default, raw, jsonl]. raw
                         suppresses log labels and timestamp.
  -z, --timezone=Local   Specify the timezone to use when formatting output
                         timestamps [Local, UTC]
      --addr="http://localhost:3100"
                         Server address. Can also be set using LOKI_ADDR env
                         var.
      --username=""      Username for HTTP basic auth. Can also be set using
                         LOKI_USERNAME env var.
      --password=""      Password for HTTP basic auth. Can also be set using
                         LOKI_PASSWORD env var.
      --ca-cert=""       Path to the server Certificate Authority. Can also be
                         set using LOKI_CA_CERT_PATH env var.
      --tls-skip-verify  Server certificate TLS skip verify.
      --cert=""          Path to the client certificate. Can also be set using
                         LOKI_CLIENT_CERT_PATH env var.
      --key=""           Path to the client certificate key. Can also be set
                         using LOKI_CLIENT_KEY_PATH env var.
      --org-id=ORG-ID    adds X-Scope-OrgID to API requests for representing
                         tenant ID. Useful for requesting tenant data when
                         bypassing an auth gateway.

Commands:
  help [<command>...]
    Show help.

  query [<flags>] <query>
    Run a LogQL query.

    The "query" command is useful for querying for logs. Logs can be returned in
    a few output modes:

      raw: log line
      default: log timestamp + log labels + log line
      jsonl: JSON response from Loki API of log line

    The output of the log can be specified with the "-o" flag, for example, "-o
    raw" for the raw output format.

    The "query" command will output extra information about the query and its
    results, such as the API URL, set of common labels, and set of excluded
    labels. This extra information can be suppressed with the --quiet flag.

    While "query" does support metrics queries, its output contains multiple
    data points between the start and end query time. This output is used to
    build graphs, like what is seen in the Grafana Explore graph view. If you
    are querying metrics and just want the most recent data point (like what is
    seen in the Grafana Explore table view), then you should use the
    "instant-query" command instead.

  instant-query [<flags>] <query>
    Run an instant LogQL query.

    The "instant-query" command is useful for evaluating a metric query for a
    single point in time. This is equivalent to the Grafana Explore table view;
    if you want a metrics query that is used to build a Grafana graph, you
    should use the "query" command instead.

    This command does not produce useful output when querying for log lines; you
    should always use the "query" command when you are running log queries.

    For more information about log queries and metric queries, refer to the
    LogQL documentation:

    https://github.com/grafana/loki/blob/master/docs/logql.md

  labels [<label>]
    Find values for a given label.

$ logcli help query
usage: logcli query [<flags>] <query>

Run a LogQL query.

The "query" command is useful for querying for logs. Logs can be returned in a
few output modes:

  raw: log line
  default: log timestamp + log labels + log line
  jsonl: JSON response from Loki API of log line

The output of the log can be specified with the "-o" flag, for example, "-o raw"
for the raw output format.

The "query" command will output extra information about the query and its
results, such as the API URL, set of common labels, and set of excluded labels.
This extra information can be suppressed with the --quiet flag.

While "query" does support metrics queries, its output contains multiple data
points between the start and end query time. This output is used to build
graphs, like what is seen in the Grafana Explore graph view. If you are querying
metrics and just want the most recent data point (like what is seen in the
Grafana Explore table view), then you should use the "instant-query" command
instead.

Flags:
      --help             Show context-sensitive help (also try --help-long and
                         --help-man).
      --version          Show application version.
  -q, --quiet            suppress query metadata
      --stats            show query statistics
  -o, --output=default   specify output mode [default, raw, jsonl]. raw
                         suppresses log labels and timestamp.
  -z, --timezone=Local   Specify the timezone to use when formatting output
                         timestamps [Local, UTC]
      --addr="http://localhost:3100"
                         Server address. Can also be set using LOKI_ADDR env
                         var.
      --username=""      Username for HTTP basic auth. Can also be set using
                         LOKI_USERNAME env var.
      --password=""      Password for HTTP basic auth. Can also be set using
                         LOKI_PASSWORD env var.
      --ca-cert=""       Path to the server Certificate Authority. Can also be
                         set using LOKI_CA_CERT_PATH env var.
      --tls-skip-verify  Server certificate TLS skip verify.
      --cert=""          Path to the client certificate. Can also be set using
                         LOKI_CLIENT_CERT_PATH env var.
      --key=""           Path to the client certificate key. Can also be set
                         using LOKI_CLIENT_KEY_PATH env var.
      --org-id=ORG-ID    adds X-Scope-OrgID to API requests for representing
                         tenant ID. Useful for requesting tenant data when
                         bypassing an auth gateway.
      --limit=30         Limit on number of entries to print.
      --since=1h         Lookback window.
      --from=FROM        Start looking for logs at this absolute time
                         (inclusive)
      --to=TO            Stop looking for logs at this absolute time (exclusive)
      --step=STEP        Query resolution step width
      --forward          Scan forwards through logs.
      --local-config=""  Execute the current query using a configured storage from a given Loki configuration file.
      --no-labels        Do not print any labels
      --exclude-label=EXCLUDE-LABEL ...
                         Exclude labels given the provided key during output.
      --include-label=INCLUDE-LABEL ...
                         Include labels given the provided key during output.
      --labels-length=0  Set a fixed padding to labels
  -t, --tail             Tail the logs
      --delay-for=0      Delay in tailing by number of seconds to accumulate
                         logs for re-ordering

Args:
  <query>  eg '{foo="bar",baz=~".*blip"} |~ ".*error.*"'
```