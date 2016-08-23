+++
draft = false
title = "Input Components"
weight = 3
+++

Input components provide different methods for obtaining incoming log data.

## Standard Input

Stdin is an input component that reads from standard input. There are **no** required or optional
configuration options.

## Nats

Nats is an input component that reads from a Nats queue.

### Configuration

 * Required
   * url - URL of the nats server - nats://127.0.0.1:4222
   * subscribe - The name of the queue to subscribe to
 * Optional
   * servers - List of alternative servers for Nats
   * encoding - The encoding to use, defaults to "json" (json|gob|default)

## TCP

TCP is an input component that listens for TCP connections.

### Configuration

 * Required
   * listen - The IP address port pair (ex: 127.0.0.1:5555, :5555)

### Extra Fields

 * tcp.source - The connection remote address that is added to all incoming messages

## UDP

UDP is an input component that listens for UDP connections.

### Configuration

 * Required
   * listen - The IP address port pair (ex: 127.0.0.1:5555, :5555)

### Extra Fields

 * udp.source - The connection remote address that is added to all incoming messages

## Syslog

Syslog is a UDP based input component for reading in syslog messages.

The 'message' field is everything after the syslog message header.

### Configuration

 * Required:
   * listen - The IP address port pair (ex: 127.0.0.1:5555, :5555)

### Extra Fields

 * udp.source - The connection remote address that is added to all incoming messages
 * syslog.tag - The tag in the syslog header
 * syslog.hostname - the hostname in the syslog header
 * syslog.timestamp - the timestamp in the syslog header
 * syslog.pri - the PRI values (unparsed) in the syslog header

