+++
draft = false
title = "Documentation"
weight = 2
+++

<h2 id="installation">Installation</h2>

<h3 id="installing-quicklog">Installing quicklog</h3>

Installing quicklog can be done by downloading the latest binary [here](#) or performing 'go install':

	$ go install github.com/sheenobu/quicklog/cmd/quicklog

<h2 id="configuration">Configuration</h2>

quicklog provides two separate means of configuration: Loading from a file or loading from an etcd cluster.

<h3 id="file">File based configuration</h3>

File based configuration is based on JSON. The chain is described within the JSON
alongside the configuration for each component.

Running with a file based configuration:

	$ quicklog -filename quicklog.json

quicklog.json:

	{
		"input": {
			"driver":"stdin"
		},
		"filters": [
			{
				"driver":"uppercase"
			}
		],
		"output": {
			"driver":"stdout"
		}
	}

Filters is **optional**, however both **input** and **output** are required.

Required and optional configuration is provided via the 'config' field:

	{
		"input": {
			"driver":"nats",
			"config": {
				"url": "nats://127.0.0.1:4222",
				"subscribe": "logging:queue"
			}
	}

Parsers are provided via the 'parser' field on 'input':

	{
		"input": {
			"driver":"stdin",
			"parser": "otto",
			"config": {
				"otto.script": "res=function(e){return l=e.split(\" \"),{message:l[0],meta:l[1]}};"
			}
		}
	}

<h3 id="etcd">etcd Based Configuration</h3>

The etcd based configuration works nearly identifical to the JSON configuration. However, instead of a JSON
file, each entry is stored in etcd as keys. The best way to explore this is by downloading ql2etcd,
then loading a JSON based configuration into etcd:

	$ go install github.com/sheenobu/quicklog/cmd/ql2etcd
	$ ql2etcd -input quicklog.json -instanceName instanceName -etcdEndpoints http://localhost:4001

	$ etcdctl ls --recursive
	/quicklog
	/quicklog/instanceName
	/quicklog/instanceName/filters
	/quicklog/instanceName/filters/0
	/quicklog/instanceName/filters/0/driver
	/quicklog/instanceName/filters/0/config
	/quicklog/instanceName/reload
	/quicklog/instanceName/input
	/quicklog/instanceName/input/driver
	/quicklog/instanceName/input/parser
	/quicklog/instanceName/input/config
	/quicklog/instanceName/output
	/quicklog/instanceName/output/config
	/quicklog/instanceName/output/driver

Each driver, parser entry is a string type. However, the 'config' fields are JSON types:

	$ etcdctl get /quicklog/instanceName/input/config
	{"otto.script":"res=function(e){return l=e.split(\" \"),{message:l[0],meta:l[1]}};"}

Running this etcd config with quicklog:

	$  quicklog -instanceName instanceName -etcdEndpoints http://localhost:4001

Programmative reloading of the etcd configuration on a running quicklog instance can be
done via setting the `/quicklog/instanceName/reload` field after updating the required entries.

<h2 id="clustering">Clustering</h2>

There has been little upfront through to clustering in quicklog. However, deploying multiple instances of quicklog can be
done in many ways:

 * Have each quicklog instance pull from a Nats queue, each instance getting a subset of messages which can be later merged together (via elasticsearch, bleve, etc).
 * The input of one quicklog instance can be the output of another quicklog, creating a tree of quicklog instances.
 * Quicklog can be embedded, made to be specialized as a specific component within a larger log aggregation architecture.

<h2 id="embedding">Embedding</h2>

The primary package of quicklog is `github.com/sheenobu/quicklog/ql`. This package is the primary implementation of the
component system and quicklog chain. Embedding requires creating a chain, adding each required configuration option, and running the chain:

	import (
		"github.com/sheenobu/quicklog/filters/uuid"
		"github.com/sheenobu/quicklog/inputs/stdin"
		"github.com/sheenobu/quicklog/outputs/debug"
		"github.com/sheenobu/quicklog/parsers/plain"

		"golang.org/x/net/context"

		"github.com/sheenobu/quicklog/ql"
	)

	func main() {
		chain := ql.Chain{
			Input: &stdin.Process{},
			Output: &debug.Handler{PrintFields: debug.NullableBool{NotNull: false, Value: true}},
			Filter: &uuid.Handler{FieldName: "uuid"},
		}

		ctx := context.Background()
		chain.Execute(ctx)
	}

This example defines our chain by importing a bunch of pre-build components. We can also provide custom components:

	chain.Input = ql.InputProcessFunc(func(ctx context.Context, input chan<- ql.Buffer) error {
		go func() {
				for {
						select {
						case t := <-time.After(5 * time.Second):
								input <- ql.Buffer{Data: []byte("hello + " + t.String())}
						case <-ctx.Done():
								return
						}
				}
		}()

		return nil
})


