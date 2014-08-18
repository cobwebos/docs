## <a name="what-is"> </a>什么是 Blob 存储

Windows Azure Blob 存储是一项可存储大量非结构化数据
的服务，用户可在世界任何地方通过 HTTP 或 HTTPS 访问
这些数据。单个 Blob 可包含数百 GB，单个存储帐户
最多可包含 200TB 的 Blob，前提是它是在 2012 年 6 月 8 日
或之后创建的；此日期之前创建的存储帐户最多可包含
100TB 的 Blob。有关存储帐户容量的详细信息，请参见 [Windows Azure 存储可伸缩性和性能目标](http://msdn.microsoft.com/zh-cn/library/dn249410.aspx)。

Blob 存储的常见用途包括：

-   直接为浏览器提供图像或文档
-   存储文件以供分布式访问
-   流式传输视频和音频
-   执行安全备份和灾难恢复
-   存储数据以供本地或 Windows Azure 托管服务执行分析

您可以使用 Blob 存储向外公布数据，或者私下公布数据以供在
内部存储应用程序。

## <a name="concepts"> </a>概念

Blob 服务包含以下组件：

![Blob1][Blob1]

-   **存储帐户**：对 Windows Azure 存储服务的所有访问都要通过存储帐户完成。有关存储帐户容量的详细信息，请参见 [Windows Azure 存储可伸缩性和性能目标](http://msdn.microsoft.com/zh-cn/library/dn249410.aspx)。

-   **容器**：一个容器包含一组 blob 集。
    所有 blob 必须位于相应的容器中。一个帐户可以包含无限个容器。一个容器可以存储无限个 blob。

-   **Blob**：任何类型和大小的文件。可将两类 Blob 存储到 Windows Azure 存储服务中：
块 Blob 和页 Blob。
    大部分文件都是块 blob。一个块 Blob 的大小可以达到 200 GB。本教程使用的是块 Blob。另一种 Blob 类型为页 Blob，
其大小可以达 1 TB，在对文件中的一系列字节进行频繁修改时，
这种 Blob 类型更加高效。有关 Blob 的更多信息，
请参见[了解块 Blob 和页 Blob][]。

-   **URL 格式**：使用以下 URL 格式对 Blob
    寻址：   
    http://`<storage
    account>`.blob.core.windows.net/`<container>`/`<blob>`  
      
    以下示例 URL 可用于对上图中的某个 Blob
    寻址： 
    `http://sally.blob.core.windows.net/movies/MOV1.AVI`


  [了解块 Blob 和页 Blob]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ee691964.aspx
[Blob1]: ./media/howto-blob-storage/blob1.jpg

