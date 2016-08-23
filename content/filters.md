+++
draft = false
title = "Filters"
weight = 5
+++

Filters are components which modify the message data.

## uuid

UUID adds a uuid v4 as a field to the message data. 

### Configuration

 * Optional
   * field - field to add the uuid value to (defaults to 'uuid')

## uppercase

Uppercase performs uppercase on the 'message' field.

There are **no** required or optional configuration options.

## rename\_field

Rename field renames the given field, if it exists.

### Configuration

 * Required
   * source - The field to rename
   * dest - The new name of the field
 * Optional
   * copy - (true|false) - whether to copy or rename the field

## hostname

Hostname adds the hostname of the current system (os.Hostname) to the given field

### Configuration

 * Optional
  * field - field to add the hostname to (defaults to 'hostname')

