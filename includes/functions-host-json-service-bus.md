```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|maxConcurrentCalls|16|消息泵应该对回调发起的最大并发调用数。 默认情况下，Functions 运行时同时处理多条消息。 若要指示运行时一次只处理单个队列或主题消息，请将 `maxConcurrentCalls` 设置为 1。 | 
|prefetchCount|不适用|基础 MessageReceiver 将要使用的默认 PrefetchCount。| 
|autoRenewTimeout|00:05:00|自动续订消息锁的最长持续时间。| 
