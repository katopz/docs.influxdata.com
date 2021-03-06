---
title: K8sAutoscaleNode
note: Auto generated by tickdoc

menu:
  kapacitor_1_1:
    name: K8sAutoscale
    identifier: k8s_autoscale_node
    weight: 150
    parent: nodes
---

[K8sAutoscaleNode](/kapacitor/v1.1/nodes/k8s_autoscale_node/) triggers autoscale events for a resource on a Kubernetes cluster. 
The node also outputs points for the triggered events. 

Example: 


```javascript
     // Target 100 requests per second per host
     var target = 100.0
     var min = 1
     var max = 100
     var period = 5m
     var every = period
     stream
         |from()
             .measurement('requests')
             .groupBy('host', 'deployment')
             .truncate(1s)
         |derivative('value')
             .as('requests_per_second')
             .unit(1s)
             .nonNegative()
         |groupBy('deployment')
         |sum('requests_per_second')
             .as('total_requests')
         |window()
             .period(period)
             .every(every)
         |mean('total_requests')
             .as('total_requests')
         |k8sAutoscale()
             // Get the name of the deployment from the 'deployment' tag.
             .resourceNameTag('deployment')
             .min(min)
             .max(max)
             // Set the desired number of replicas based on target.
             .replicas(lambda: int(ceil("total_requests" / target)))
         |influxDBOut()
             .database('deployments')
             .measurement('scale_events')
             .precision('s')
```


The above example computes the requests per second by deployment and host. 
Then the total_requests per second across all hosts is computed per deployment. 
Using the mean of the total_requests over the last time period a desired number of replicas is computed 
based on the target number of request per second per host. 

If the desired number of replicas has changed, Kapacitor makes the appropriate API call to Kubernetes 
to update the replicas spec. 

Any time the k8sAutoscale node changes a replica count, it emits a point. 
The point is tagged with the namespace, kind and resource name, 
using the NamespaceTag, KindTag, and ResourceTag properties respectively. 
In addition the group by tags will be preserved on the emitted point. 
The point contains two fields: `old`, and `new` representing change in the replicas. 

Available Statistics: 

* increase_events -- number of times the replica count was increased. 
* decrease_events -- number of times the replica count was decreased. 
* cooldown_drops -- number of times an event was dropped because of a cooldown timer. 
* errors -- number of errors encountered, typically related to communicating with the Kubernetes API. 



Index
-----

### Properties

-	[CurrentField](/kapacitor/v1.1/nodes/k8s_autoscale_node/#currentfield)
-	[DecreaseCooldown](/kapacitor/v1.1/nodes/k8s_autoscale_node/#decreasecooldown)
-	[IncreaseCooldown](/kapacitor/v1.1/nodes/k8s_autoscale_node/#increasecooldown)
-	[Kind](/kapacitor/v1.1/nodes/k8s_autoscale_node/#kind)
-	[KindTag](/kapacitor/v1.1/nodes/k8s_autoscale_node/#kindtag)
-	[Max](/kapacitor/v1.1/nodes/k8s_autoscale_node/#max)
-	[Min](/kapacitor/v1.1/nodes/k8s_autoscale_node/#min)
-	[Namespace](/kapacitor/v1.1/nodes/k8s_autoscale_node/#namespace)
-	[NamespaceTag](/kapacitor/v1.1/nodes/k8s_autoscale_node/#namespacetag)
-	[Replicas](/kapacitor/v1.1/nodes/k8s_autoscale_node/#replicas)
-	[ResourceName](/kapacitor/v1.1/nodes/k8s_autoscale_node/#resourcename)
-	[ResourceNameTag](/kapacitor/v1.1/nodes/k8s_autoscale_node/#resourcenametag)
-	[ResourceTag](/kapacitor/v1.1/nodes/k8s_autoscale_node/#resourcetag)

### Chaining Methods

-	[Alert](/kapacitor/v1.1/nodes/k8s_autoscale_node/#alert)
-	[Bottom](/kapacitor/v1.1/nodes/k8s_autoscale_node/#bottom)
-	[Combine](/kapacitor/v1.1/nodes/k8s_autoscale_node/#combine)
-	[Count](/kapacitor/v1.1/nodes/k8s_autoscale_node/#count)
-	[CumulativeSum](/kapacitor/v1.1/nodes/k8s_autoscale_node/#cumulativesum)
-	[Deadman](/kapacitor/v1.1/nodes/k8s_autoscale_node/#deadman)
-	[Default](/kapacitor/v1.1/nodes/k8s_autoscale_node/#default)
-	[Delete](/kapacitor/v1.1/nodes/k8s_autoscale_node/#delete)
-	[Derivative](/kapacitor/v1.1/nodes/k8s_autoscale_node/#derivative)
-	[Difference](/kapacitor/v1.1/nodes/k8s_autoscale_node/#difference)
-	[Distinct](/kapacitor/v1.1/nodes/k8s_autoscale_node/#distinct)
-	[Elapsed](/kapacitor/v1.1/nodes/k8s_autoscale_node/#elapsed)
-	[Eval](/kapacitor/v1.1/nodes/k8s_autoscale_node/#eval)
-	[First](/kapacitor/v1.1/nodes/k8s_autoscale_node/#first)
-	[Flatten](/kapacitor/v1.1/nodes/k8s_autoscale_node/#flatten)
-	[GroupBy](/kapacitor/v1.1/nodes/k8s_autoscale_node/#groupby)
-	[HoltWinters](/kapacitor/v1.1/nodes/k8s_autoscale_node/#holtwinters)
-	[HoltWintersWithFit](/kapacitor/v1.1/nodes/k8s_autoscale_node/#holtwinterswithfit)
-	[HttpOut](/kapacitor/v1.1/nodes/k8s_autoscale_node/#httpout)
-	[InfluxDBOut](/kapacitor/v1.1/nodes/k8s_autoscale_node/#influxdbout)
-	[Join](/kapacitor/v1.1/nodes/k8s_autoscale_node/#join)
-	[K8sAutoscale](/kapacitor/v1.1/nodes/k8s_autoscale_node/#k8sautoscale)
-	[Last](/kapacitor/v1.1/nodes/k8s_autoscale_node/#last)
-	[Log](/kapacitor/v1.1/nodes/k8s_autoscale_node/#log)
-	[Mean](/kapacitor/v1.1/nodes/k8s_autoscale_node/#mean)
-	[Median](/kapacitor/v1.1/nodes/k8s_autoscale_node/#median)
-	[Mode](/kapacitor/v1.1/nodes/k8s_autoscale_node/#mode)
-	[MovingAverage](/kapacitor/v1.1/nodes/k8s_autoscale_node/#movingaverage)
-	[Percentile](/kapacitor/v1.1/nodes/k8s_autoscale_node/#percentile)
-	[Sample](/kapacitor/v1.1/nodes/k8s_autoscale_node/#sample)
-	[Shift](/kapacitor/v1.1/nodes/k8s_autoscale_node/#shift)
-	[Spread](/kapacitor/v1.1/nodes/k8s_autoscale_node/#spread)
-	[Stats](/kapacitor/v1.1/nodes/k8s_autoscale_node/#stats)
-	[Stddev](/kapacitor/v1.1/nodes/k8s_autoscale_node/#stddev)
-	[Sum](/kapacitor/v1.1/nodes/k8s_autoscale_node/#sum)
-	[Top](/kapacitor/v1.1/nodes/k8s_autoscale_node/#top)
-	[Union](/kapacitor/v1.1/nodes/k8s_autoscale_node/#union)
-	[Where](/kapacitor/v1.1/nodes/k8s_autoscale_node/#where)
-	[Window](/kapacitor/v1.1/nodes/k8s_autoscale_node/#window)

Properties
----------

Property methods modify state on the calling node.
They do not add another node to the pipeline, and always return a reference to the calling node.
Property methods are marked using the `.` operator.


### CurrentField

CurrentField is the name of a field into which the current replica count will be set as an int. 
If empty no field will be set. 
Useful for computing deltas on the current state. 

Example: 


```javascript
    |k8sAutoscale()
        .currentField('replicas')
        // Increase the replicas by 1 if the qps is over the threshold
        .replicas(lambda: if("qps" > threshold, "replicas" + 1, "replicas"))
```



```javascript
node.currentField(value string)
```


### DecreaseCooldown

Only one decrease event can be triggered per resource every DecreaseCooldown interval. 


```javascript
node.decreaseCooldown(value time.Duration)
```


### IncreaseCooldown

Only one increase event can be triggered per resource every IncreaseCooldown interval. 


```javascript
node.increaseCooldown(value time.Duration)
```


### Kind

Kind is the type of resources to autoscale. 
Currently only &#34;deployments&#34;, &#34;replicasets&#34; and &#34;replicationcontrollers&#34; are supported. 
Default: &#34;deployments&#34; 


```javascript
node.kind(value string)
```


### KindTag

KindTag is the name of a tag to use when tagging emitted points with the kind. 
If empty the point will not be tagged with the resource. 
Default: kind 


```javascript
node.kindTag(value string)
```


### Max

The maximum scale factor to set. 
If 0 then there is no upper limit. 
Default: 0, a.k.a no limit. 


```javascript
node.max(value int64)
```


### Min

The minimum scale factor to set. 
Default: 1 


```javascript
node.min(value int64)
```


### Namespace

Namespace is the namespace of the resource, if empty the default namespace will be used. 


```javascript
node.namespace(value string)
```


### NamespaceTag

NamespaceTag is the name of a tag to use when tagging emitted points with the namespace. 
If empty the point will not be tagged with the resource. 
Default: namespace 


```javascript
node.namespaceTag(value string)
```


### Replicas

Replicas is a lambda expression that should evaluate to the desired number of replicas for the resource. 


```javascript
node.replicas(value ast.LambdaNode)
```


### ResourceName

ResourceName is the name of the resource to autoscale. 


```javascript
node.resourceName(value string)
```


### ResourceNameTag

ResourceNameTag is the name of a tag that names the resource to autoscale. 


```javascript
node.resourceNameTag(value string)
```


### ResourceTag

ResourceTag is the name of a tag to use when tagging emitted points the resource. 
If empty the point will not be tagged with the resource. 
Default: resource 


```javascript
node.resourceTag(value string)
```


Chaining Methods
----------------

Chaining methods create a new node in the pipeline as a child of the calling node.
They do not modify the calling node.
Chaining methods are marked using the `|` operator.


### Alert

Create an alert node, which can trigger alerts. 


```javascript
node|alert()
```

Returns: [AlertNode](/kapacitor/v1.1/nodes/alert_node/)


### Bottom

Select the bottom `num` points for `field` and sort by any extra tags or fields. 


```javascript
node|bottom(num int64, field string, fieldsAndTags ...string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Combine

Combine this node with itself. The data are combined on timestamp. 


```javascript
node|combine(expressions ...ast.LambdaNode)
```

Returns: [CombineNode](/kapacitor/v1.1/nodes/combine_node/)


### Count

Count the number of points. 


```javascript
node|count(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### CumulativeSum

Compute a cumulative sum of each point that is received. 
A point is emitted for every point collected. 


```javascript
node|cumulativeSum(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Deadman

Helper function for creating an alert on low throughput, a.k.a. deadman&#39;s switch. 

- Threshold -- trigger alert if throughput drops below threshold in points/interval. 
- Interval -- how often to check the throughput. 
- Expressions -- optional list of expressions to also evaluate. Useful for time of day alerting. 

Example: 


```javascript
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    data
        |deadman(100.0, 10s)
    //Do normal processing of data
    data...
```

The above is equivalent to this 
Example: 


```javascript
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    data
        |stats(10s)
            .align()
        |derivative('emitted')
            .unit(10s)
            .nonNegative()
        |alert()
            .id('node \'stream0\' in task \'{{ .TaskName }}\'')
            .message('{{ .ID }} is {{ if eq .Level "OK" }}alive{{ else }}dead{{ end }}: {{ index .Fields "emitted" | printf "%0.3f" }} points/10s.')
            .crit(lambda: "emitted" <= 100.0)
    //Do normal processing of data
    data...
```

The `id` and `message` alert properties can be configured globally via the &#39;deadman&#39; configuration section. 

Since the [AlertNode](/kapacitor/v1.1/nodes/alert_node/) is the last piece it can be further modified as usual. 
Example: 


```javascript
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    data
        |deadman(100.0, 10s)
            .slack()
            .channel('#dead_tasks')
    //Do normal processing of data
    data...
```

You can specify additional lambda expressions to further constrain when the deadman&#39;s switch is triggered. 
Example: 


```javascript
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    // Only trigger the alert if the time of day is between 8am-5pm.
    data
        |deadman(100.0, 10s, lambda: hour("time") >= 8 AND hour("time") <= 17)
    //Do normal processing of data
    data...
```



```javascript
node|deadman(threshold float64, interval time.Duration, expr ...ast.LambdaNode)
```

Returns: [AlertNode](/kapacitor/v1.1/nodes/alert_node/)


### Default

Create a node that can set defaults for missing tags or fields. 


```javascript
node|default()
```

Returns: [DefaultNode](/kapacitor/v1.1/nodes/default_node/)


### Delete

Create a node that can delete tags or fields. 


```javascript
node|delete()
```

Returns: [DeleteNode](/kapacitor/v1.1/nodes/delete_node/)


### Derivative

Create a new node that computes the derivative of adjacent points. 


```javascript
node|derivative(field string)
```

Returns: [DerivativeNode](/kapacitor/v1.1/nodes/derivative_node/)


### Difference

Compute the difference between points independent of elapsed time. 


```javascript
node|difference(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Distinct

Produce batch of only the distinct points. 


```javascript
node|distinct(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Elapsed

Compute the elapsed time between points 


```javascript
node|elapsed(field string, unit time.Duration)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Eval

Create an eval node that will evaluate the given transformation function to each data point. 
A list of expressions may be provided and will be evaluated in the order they are given. 
The results are available to later expressions. 


```javascript
node|eval(expressions ...ast.LambdaNode)
```

Returns: [EvalNode](/kapacitor/v1.1/nodes/eval_node/)


### First

Select the first point. 


```javascript
node|first(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Flatten

Flatten points with similar times into a single point. 


```javascript
node|flatten()
```

Returns: [FlattenNode](/kapacitor/v1.1/nodes/flatten_node/)


### GroupBy

Group the data by a set of tags. 

Can pass literal * to group by all dimensions. 
Example: 


```javascript
    |groupBy(*)
```



```javascript
node|groupBy(tag ...interface{})
```

Returns: [GroupByNode](/kapacitor/v1.1/nodes/group_by_node/)


### HoltWinters

Compute the holt-winters forecast of a data set. 


```javascript
node|holtWinters(field string, h int64, m int64, interval time.Duration)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### HoltWintersWithFit

Compute the holt-winters forecast of a data set. 
This method also outputs all the points used to fit the data in addition to the forecasted data. 


```javascript
node|holtWintersWithFit(field string, h int64, m int64, interval time.Duration)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### HttpOut

Create an HTTP output node that caches the most recent data it has received. 
The cached data are available at the given endpoint. 
The endpoint is the relative path from the API endpoint of the running task. 
For example, if the task endpoint is at `/kapacitor/v1/tasks/&lt;task_id&gt;` and endpoint is 
`top10`, then the data can be requested from `/kapacitor/v1/tasks/&lt;task_id&gt;/top10`. 


```javascript
node|httpOut(endpoint string)
```

Returns: [HTTPOutNode](/kapacitor/v1.1/nodes/http_out_node/)


### InfluxDBOut

Create an influxdb output node that will store the incoming data into InfluxDB. 


```javascript
node|influxDBOut()
```

Returns: [InfluxDBOutNode](/kapacitor/v1.1/nodes/influx_d_b_out_node/)


### Join

Join this node with other nodes. The data are joined on timestamp. 


```javascript
node|join(others ...Node)
```

Returns: [JoinNode](/kapacitor/v1.1/nodes/join_node/)


### K8sAutoscale

Create a node that can trigger autoscale events for a kubernetes cluster. 


```javascript
node|k8sAutoscale()
```

Returns: [K8sAutoscaleNode](/kapacitor/v1.1/nodes/k8s_autoscale_node/)


### Last

Select the last point. 


```javascript
node|last(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Log

Create a node that logs all data it receives. 


```javascript
node|log()
```

Returns: [LogNode](/kapacitor/v1.1/nodes/log_node/)


### Mean

Compute the mean of the data. 


```javascript
node|mean(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Median

Compute the median of the data. Note, this method is not a selector, 
if you want the median point use `.percentile(field, 50.0)`. 


```javascript
node|median(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Mode

Compute the mode of the data. 


```javascript
node|mode(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### MovingAverage

Compute a moving average of the last window points. 
No points are emitted until the window is full. 


```javascript
node|movingAverage(field string, window int64)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Percentile

Select a point at the given percentile. This is a selector function, no interpolation between points is performed. 


```javascript
node|percentile(field string, percentile float64)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Sample

Create a new node that samples the incoming points or batches. 

One point will be emitted every count or duration specified. 


```javascript
node|sample(rate interface{})
```

Returns: [SampleNode](/kapacitor/v1.1/nodes/sample_node/)


### Shift

Create a new node that shifts the incoming points or batches in time. 


```javascript
node|shift(shift time.Duration)
```

Returns: [ShiftNode](/kapacitor/v1.1/nodes/shift_node/)


### Spread

Compute the difference between `min` and `max` points. 


```javascript
node|spread(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Stats

Create a new stream of data that contains the internal statistics of the node. 
The interval represents how often to emit the statistics based on real time. 
This means the interval time is independent of the times of the data points the source node is receiving. 


```javascript
node|stats(interval time.Duration)
```

Returns: [StatsNode](/kapacitor/v1.1/nodes/stats_node/)


### Stddev

Compute the standard deviation. 


```javascript
node|stddev(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Sum

Compute the sum of all values. 


```javascript
node|sum(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Top

Select the top `num` points for `field` and sort by any extra tags or fields. 


```javascript
node|top(num int64, field string, fieldsAndTags ...string)
```

Returns: [InfluxQLNode](/kapacitor/v1.1/nodes/influx_q_l_node/)


### Union

Perform the union of this node and all other given nodes. 


```javascript
node|union(node ...Node)
```

Returns: [UnionNode](/kapacitor/v1.1/nodes/union_node/)


### Where

Create a new node that filters the data stream by a given expression. 


```javascript
node|where(expression ast.LambdaNode)
```

Returns: [WhereNode](/kapacitor/v1.1/nodes/where_node/)


### Window

Create a new node that windows the stream by time. 

NOTE: Window can only be applied to stream edges. 


```javascript
node|window()
```

Returns: [WindowNode](/kapacitor/v1.1/nodes/window_node/)

