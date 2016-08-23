+++
draft = false
title = "Output Components"
weight = 6
+++

Output Components provide methods for writing the processed log data somewhere
for analysis or further processing.


## Standard Output

Stdout is an output component that writes to standard output. There are **no** required or optional
configuration options.

## Debug

Debug is an output component that writes to standard output with some debug information.

Writes both the Timestamp and the Message field.

### Configuration

 * Optional
   * print-fields - (true|false) - Whether to print all the fields of the log entry or just the 'message' field.

## Nats

Nats is an output component that writes to a Nats queue.

### Configuration

 * Required:
   * url - URL of the nats server.
   * publish - Output queue to publish the messages on.
 * Optional:
   * encoding - The encoding to use (defaults to json) (json|gob|default).
   * servers - List of alternate Nats servers.

## elasticsearch-http

Elasticsearch HTTP is an output component that writes to an elasticsearch HTTP server.

### Configuration

 * Required:
   * url - URL of the elasticsearch server
 * Optional:
   * index - Elasticsearch index (defaults to quicklog)
   * type - type of Elasticsearch entry (defaults to 'entry')
