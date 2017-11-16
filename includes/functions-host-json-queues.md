```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|maxPollingInterval|60000|队列轮询的最大间隔时间，以毫秒为单位。| 
|visibilityTimeout|0|消息处理失败时的重试间隔时间。| 
|batchSize|16|要并行检索和处理的队列消息数。 最大值为 32。| 
|maxDequeueCount|5|在将某个消息移到有害队列之前，尝试处理该消息的次数。| 
|newBatchThreshold|batchSize/2|提取新消息批所要达到的阈值。| 
