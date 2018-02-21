Azure Functions [触发器和绑定](..\articles\azure-functions\functions-triggers-bindings.md)与各种 Azure 服务进行通信。 与这些服务集成时，可能会遇到来源于底层 Azure 服务 API 的引发错误。 尝试使用 REST 或客户端库通过函数代码与其他服务进行通信时，也可能会发生错误。 若要避免数据丢失并确保函数的行为正常，请务必处理来自任一源的错误。

对于大多数触发器而言，函数执行期间发生错误时没有内置重试机制。 支持重试的两种触发器是 Azure 队列存储和 Azure Blob 存储。 默认情况下，这两种触发器最多重试五次。 第五次重试后，这两种触发器都会将消息写入到一个特殊的[有害队列](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages)。 

为了防止在函数中发生错误时触发器信息丢失，我们建议你在函数代码中使用 try-catch 块捕获任何错误。 发生错误时，触发器会将传入函数的信息写入到特殊的“有害”消息队列。 [Blob 存储触发器](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs)也使用此方法。 

这样，可以捕获因出错可能会丢失的触发器事件，并在稍后通过其他函数使用存储的信息重试处理有害队列中的消息。  