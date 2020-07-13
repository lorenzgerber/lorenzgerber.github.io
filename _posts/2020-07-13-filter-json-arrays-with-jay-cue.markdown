---
layout: post
title:  "Filter json arrays with jay cue"
date:   2020-07-13 09:40:00 +0200
categories: shell bash cli jq
---

## Background
When working extensively on awscli, often the situation arises that one needs to filter an array of json elements that is returned from an awscli command. `jq` is the tool of choice to accomplish that. Below an example that includes a number of different features from `jq` to obtain a specifc aws batch job definition record.

## example
```
aws batch describe-job-definitions | jq '.[] | .[] |  \\
select( (.status == "ACTIVE") \\
and (.containerProperties | .environment | .[] | .value == "HEATMAP") \\
and (.jobDefinitionName | contains("dev")))'
```
The additional parameters are provided in single quotes. Most awscli json return objects come with two enclosing arrays, hence `.[] | .[]` extracts the json records for further filtering.  
`select()` will return all records that evaluate to `TRUE` in the parenthesis enclosed condition. Often these conditions are string matching of key/value pairs. Above, the `.status` key needs to contain the value `ACTIVE` to evaluate to `TRUE`. Within the parenthesis, conditions can be chained with `and`/`or` statements. To dive into a json structure for evaluating a specifc field, one needs to use parenthesis properly. The `contains()` statement allows for matching substrings (eg. partial match). 

