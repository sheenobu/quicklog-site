+++
draft = false
title = "Parsers"
weight = 4
+++

Parsers work alongside the Input Components to determine how to parse the incoming message.

## Plain

Plain treats every incoming line as a single message, and adds it to the 'message' field.

There are **no** required or optional configuration options.

## JSON

JSON treats every incoming line as a single JSON body. Each key in the JSON becomes a
key in the quicklog message object. If the 'message' field is not present in
the JSON object, it is set as an empty string.

There are **no** required or optional configuration options.

NOTE: If your json objects come in without 'message', you can use the rename\_field filter
to make it 'message'.

## Otto

Otto runs each incoming line through a Javascript function.

The return value of the javascript function MUST be a Dictionary. The quicklog
message data is populated from this dictionary.

Example function which splits the message in two:

	res=function(e){ return l=e.split(\" \"),{message:l[0],meta:l[1]}}; "

The first part of the incoming message becomes the 'message' field and the last part becomes
the 'meta' field.

### Configuration

 * Required
   * otto.script - The javascript code to run the log message through.


