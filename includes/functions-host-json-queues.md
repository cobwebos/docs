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
|batchSize|16|Functions 运行时同时检索并并行处理的队列消息数。 当处理的数量下降到 `newBatchThreshold` 时，运行时可获取另一个批，并开始处理这些消息。 因此，每个函数处理的最大并发消息数是 `batchSize` 加上 `newBatchThreshold`。 此限制分别应用于各个队列触发的函数。 <br><br>如果要避免对队列上收到的消息并行执行，可以将 `batchSize` 设置为 1。 但是，只有在函数于单个虚拟机 (VM) 上运行时，此设置才可消除并发。 如果函数应用横向扩展到多个 VM，每个 VM 可运行每个队列触发的函数的一个实例。<br><br>`batchSize` 的最大值为 32。 | 
|maxDequeueCount|5|在将某个消息移到有害队列之前，尝试处理该消息的次数。| 
|newBatchThreshold|batchSize/2|只要同时处理的消息数下降到此数值，运行时即检索另一个批次。| 



