# Kafka Consumer Input Plugin

The [Kafka][kafka] consumer plugin reads from Kafka
and creates metrics using one of the supported [input data formats][].

For old kafka version (< 0.8), please use the [kafka_consumer_legacy][] input
plugin and use the old zookeeper connection method.

## Global configuration options <!-- @/docs/includes/plugin_config.md -->

In addition to the plugin-specific configuration settings, plugins support
additional global and plugin configuration settings. These settings are used to
modify metrics, tags, and field or create aliases and configure ordering, etc.
See the [CONFIGURATION.md][CONFIGURATION.md] for more details.

[CONFIGURATION.md]: ../../../docs/CONFIGURATION.md

## Configuration

```toml @sample.conf
# Read metrics from Kafka topics
[[inputs.kafka_consumer]]
  ## Kafka brokers.
  brokers = ["localhost:9092"]

  ## Topics to consume.
  topics = ["telegraf"]

  ## When set this tag will be added to all metrics with the topic as the value.
  # topic_tag = ""

  ## Optional Client id
  # client_id = "Telegraf"

  ## Set the minimal supported Kafka version.  Setting this enables the use of new
  ## Kafka features and APIs.  Must be 0.10.2.0 or greater.
  ##   ex: version = "1.1.0"
  # version = ""

  ## Optional TLS Config
  # enable_tls = false
  # tls_ca = "/etc/telegraf/ca.pem"
  # tls_cert = "/etc/telegraf/cert.pem"
  # tls_key = "/etc/telegraf/key.pem"
  ## Use TLS but skip chain & host verification
  # insecure_skip_verify = false

  ## Period between keep alive probes.
  ## Defaults to the OS configuration if not specified or zero.
  # keep_alive_period = "15s"

  ## SASL authentication credentials.  These settings should typically be used
  ## with TLS encryption enabled
  # sasl_username = "kafka"
  # sasl_password = "secret"

  ## Optional SASL:
  ## one of: OAUTHBEARER, PLAIN, SCRAM-SHA-256, SCRAM-SHA-512, GSSAPI
  ## (defaults to PLAIN)
  # sasl_mechanism = ""

  ## used if sasl_mechanism is GSSAPI (experimental)
  # sasl_gssapi_service_name = ""
  # ## One of: KRB5_USER_AUTH and KRB5_KEYTAB_AUTH
  # sasl_gssapi_auth_type = "KRB5_USER_AUTH"
  # sasl_gssapi_kerberos_config_path = "/"
  # sasl_gssapi_realm = "realm"
  # sasl_gssapi_key_tab_path = ""
  # sasl_gssapi_disable_pafxfast = false

  ## used if sasl_mechanism is OAUTHBEARER (experimental)
  # sasl_access_token = ""

  ## SASL protocol version.  When connecting to Azure EventHub set to 0.
  # sasl_version = 1

  # Disable Kafka metadata full fetch
  # metadata_full = false

  ## Name of the consumer group.
  # consumer_group = "telegraf_metrics_consumers"

  ## Compression codec represents the various compression codecs recognized by
  ## Kafka in messages.
  ##  0 : None
  ##  1 : Gzip
  ##  2 : Snappy
  ##  3 : LZ4
  ##  4 : ZSTD
  # compression_codec = 0
  ## Initial offset position; one of "oldest" or "newest".
  # offset = "oldest"

  ## Consumer group partition assignment strategy; one of "range", "roundrobin" or "sticky".
  # balance_strategy = "range"

  ## Maximum number of retries for metadata operations including
  ## connecting. Sets Sarama library's Metadata.Retry.Max config value. If 0 or
  ## unset, use the Sarama default of 3,
  # metadata_retry_max = 0

  ## Type of retry backoff. Valid options: "constant", "exponential"
  # metadata_retry_type = "constant"

  ## Amount of time to wait before retrying. When metadata_retry_type is
  ## "constant", each retry is delayed this amount. When "exponential", the
  ## first retry is delayed this amount, and subsequent delays are doubled. If 0
  ## or unset, use the Sarama default of 250 ms
  # metadata_retry_backoff = 0

  ## Maximum amount of time to wait before retrying when metadata_retry_type is
  ## "exponential". Ignored for other retry types. If 0, there is no backoff
  ## limit.
  # metadata_retry_max_duration = 0

  ## Strategy for making connection to kafka brokers. Valid options: "startup",
  ## "defer". If set to "defer" the plugin is allowed to start before making a
  ## connection. This is useful if the broker may be down when telegraf is
  ## started, but if there are any typos in the broker setting, they will cause
  ## connection failures without warning at startup
  # connection_strategy = "startup"

  ## Maximum length of a message to consume, in bytes (default 0/unlimited);
  ## larger messages are dropped
  max_message_len = 1000000

  ## Maximum messages to read from the broker that have not been written by an
  ## output.  For best throughput set based on the number of metrics within
  ## each message and the size of the output's metric_batch_size.
  ##
  ## For example, if each message from the queue contains 10 metrics and the
  ## output metric_batch_size is 1000, setting this to 100 will ensure that a
  ## full batch is collected and the write is triggered immediately without
  ## waiting until the next flush_interval.
  # max_undelivered_messages = 1000

  ## Maximum amount of time the consumer should take to process messages. If
  ## the debug log prints messages from sarama about 'abandoning subscription
  ## to [topic] because consuming was taking too long', increase this value to
  ## longer than the time taken by the output plugin(s).
  ##
  ## Note that the effective timeout could be between 'max_processing_time' and
  ## '2 * max_processing_time'.
  # max_processing_time = "100ms"

  ## The default number of message bytes to fetch from the broker in each
  ## request (default 1MB). This should be larger than the majority of
  ## your messages, or else the consumer will spend a lot of time
  ## negotiating sizes and not actually consuming. Similar to the JVM's
  ## `fetch.message.max.bytes`.
  # consumer_fetch_default = "1MB"

  ## Data format to consume.
  ## Each data format has its own unique set of configuration options, read
  ## more about them here:
  ## https://github.com/influxdata/telegraf/blob/master/docs/DATA_FORMATS_INPUT.md
  data_format = "influx"
```

[kafka]: https://kafka.apache.org
[kafka_consumer_legacy]: /plugins/inputs/kafka_consumer_legacy/README.md
[input data formats]: /docs/DATA_FORMATS_INPUT.md

## Metrics

The plugin accepts arbitrary input and parses it according to the `data_format`
setting. There is no predefined metric format.

## Example Output

There is no predefined metric format, so output depends on plugin input.
