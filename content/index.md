+++
draft = false
title = "Quicklog"
type = "index"
next = "documentation"
weight = 1
+++

## Introduction

*Quicklog* is a log aggregation, parser, and searcher written in Golang. It can be
run [standalone](#quick-start) or by [embedding](documentation/#embedding) into your own
programs.

Quicklog is key-value based. The messages processed by quicklog are represented as key-value pairs. By default,
incoming message bodies are stored in the 'message' field but that is not required.

## Quick start

	$ go install github.com/sheenobu/quicklog/cmd/quicklog
	$ cat <<EOF > quicklog.json
	{"input": {"driver":"stdin"}, "filters": [{"driver":"uppercase"}], "output": {"driver":"stdout"}}
	EOF
    $ quicklog
	lvl=info msg="Starting quicklog"
	lvl=info msg="Loading config from file" file=quicklog.json
	hello
	HELLO

## Components

Quicklog is a chain-based system, where data flows from an input, to a parser, to a collection of filters, then finally to an output. Each component in the chain
has a set of configuration parameters.

Additional features can be added via a <a href="https://github.com/sheenobu/quicklog">Pull Request</a> or by <a href="documentation/#embedding">Embedding</a>.


