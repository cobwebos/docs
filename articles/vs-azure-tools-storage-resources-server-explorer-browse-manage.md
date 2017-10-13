---
title: "使用服务器资源管理器浏览和管理存储资源 | Microsoft Docs"
description: "使用服务器资源管理器浏览和管理存储资源"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/24/2017
ms.author: kraigb
ms.openlocfilehash: 456daea494ed1daed9bd809b68ae12fceb800299
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="browse-and-manage-storage-resources-by-using-server-explorer"></a>使用服务器资源管理器浏览和管理存储资源
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>概述
如果已安装 Azure Tools for Microsoft Visual Studio，可以从 Azure 的存储帐户中查看 Blob、队列和表数据。 服务器资源管理器中的 Azure“存储”节点显示了位于本地存储模拟器帐户和其他 Azure 存储帐户中的数据。

若要在 Visual Studio 中查看服务器资源管理器，请在菜单栏上选择“查看” > “服务器资源管理器”。 “存储”节点显示已连接的每个 Azure 订阅或证书下的所有存储帐户。 如果存储帐户未出现，可以遵循[本主题下文中](#add-storage-accounts-by-using-server-explorer)的说明来添加帐户。

从 Azure SDK 2.7 开始，也可使用 Cloud Explorer 来查看和管理 Azure 资源。 有关详细信息，请参阅[使用 Cloud Explorer 管理 Azure 资源](vs-azure-tools-resources-managing-with-cloud-explorer.md)。

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>在 Visual Studio 中查看和管理存储资源
服务器资源管理器自动在存储模拟器帐户中显示 Blob、队列和表的列表。 存储模拟器帐户在服务器资源管理器中的“存储”节点下作为“开发”节点列出。

若要查看存储模拟器帐户的资源，请展开“开发”节点。 展开“开发”节点时，如果尚未启动存储模拟器，它会自动启动。 这可能需要数秒钟的时间。 当存储模拟器启动时，可以继续在 Visual Studio 的其他区域中工作。

若要查看存储帐户中的资源，请在服务器资源管理器中展开存储帐户的节点。 可以看到以下子节点：

* **Blob**
* **队列**
* **表**

## <a name="work-with-blob-resources"></a>处理 Blob 资源
“Blob”节点显示所选存储帐户的容器列表。 Blob 容器包含 Blob 文件，可以将这些 Blob 组织成文件夹和子文件夹。 有关详细信息，请参阅[如何通过 .NET 使用 Blob 存储](storage/blobs/storage-dotnet-how-to-use-blobs.md)。

### <a name="to-create-a-blob-container"></a>创建 Blob 容器
1. 打开“Blob”节点的快捷菜单，并选择“创建 Blob 容器”。
2. 在“创建 Blob 容器”对话框中，输入新容器的名称。  
3. 在键盘上按 Enter，或者单击或点击名称字段以外的任何位置，即可保存 Blob 容器。
   
   > [!NOTE]
   > Blob 容器名称必须以数字 (0-9) 或小写字母 (a-z) 开头。
   > 
   > 

### <a name="to-delete-a-blob-container"></a>删除 Blob 容器
* 打开要删除的 Blob 容器的快捷菜单，并选择“删除”。

### <a name="to-display-a-list-of-the-items-in-a-blob-container"></a>显示 Blob 容器中的项目列表
* 打开列表中 Blob 容器名称的快捷菜单，并选择“打开”。
  
查看 Blob 容器的内容时，该内容显示在称为“Blob 容器视图”的选项卡上。
  
![Blob 容器视图](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)
  
使用 Blob 容器视图右上角的各个按钮，可以对 Blob 执行以下操作：
  
* 输入筛选器值并应用它。
* 刷新容器中的 Blob 列表。
* 上传文件。
* 删除 Blob。
    
  > [!NOTE]
  > 从 Blob 容器中删除文件不会删除基础文件。 而只是将它从 Blob 容器删除。
  > 
  > 
* 打开 Blob。
* 将 Blob 保存到本地计算机。

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>在 Blob 容器中创建文件夹或子文件夹
1. 在云资源管理器中选择 Blob 容器。 在容器窗口中，选择“上传 Blob”按钮。
   
2. 在“上传新文件”对话框中，选择“浏览”按钮并指定要上传的文件，并在“文件夹(可选)”框中输入文件夹名称。
   
   ![将文件上传到 Blob 文件夹](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)
    
   可以遵循相同的步骤将子文件夹添加到容器文件夹。 如未指定文件夹名称，文件会上传到 Blob 容器的顶层。 文件将出现在容器中指定的文件夹内。
   
   ![已添加到 Blob 容器的文件夹](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)
3. 双击文件夹或按 Enter 查看文件夹的内容。 位于容器的文件夹中时，可以通过选择“打开父目录”（箭头）按钮返回到容器的根目录。

### <a name="to-delete-a-container-folder"></a>删除容器文件夹
* 删除文件夹中的所有文件。
  
> [!NOTE]
> 由于 Blob 容器中的文件夹是虚拟文件夹，因此无法创建空文件夹。 也无法删除文件夹并删除其文件内容。 只有删除了文件夹的全部内容才能删除文件夹本身。
> 
> 

### <a name="to-filter-blobs-in-a-container"></a>在容器中筛选 Blob
可以通过指定常用前缀来筛选显示的 Blob。

例如，如果在筛选文本框中输入前缀“hello”，并选择“执行”(!) 按钮，则只会显示以“hello”开头的 Blob。

![筛选文本框](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

> [!NOTE]
> 筛选文本框区分大小写，并且不支持使用通配符筛选。 只能按前缀筛选 Blob。 如果使用分隔符组织虚拟层次结构中的 Blob，则前缀可以包含分隔符。 例如，针对前缀“HelloFabric/”进行筛选将返回以该字符串开头的所有 Blob。
> 
> 

### <a name="to-download-blob-data"></a>下载 Blob 数据
在 Cloud Explorer 中，使用以下方法之一：
* 打开一个或多个 Blob 的快捷菜单，并选择“打开”。
* 选择 Blob 名称，并选择“打开”按钮。
* 双击 Blob 名称。
  
Blob 下载的进度会显示在“Azure 活动日志”窗口中。
  
在此文件类型的默认编辑器中打开该 Blob。 如果操作系统识别出文件类型，则在本地安装的应用程序中打开文件。 否则会提示选择适合该 Blob 文件类型的应用程序。 下载 Blob 时创建的本地文件将标记为只读。
  
Blob 数据在本地缓存，并且对照 Azure Blob 存储中 Blob 的最后修改时间检查此类数据。 如果 Blob 自上次下载以来已进行了更新，则将再次下载它。 否则，将从本地磁盘中加载 Blob。 

默认情况下，Blob 下载到临时目录中。 要将 Blob 下载到特定目录，请打开所选 Blob 名称的快捷菜单，然后选择“另存为”。 如果通过这种方式保存 Blob 时，则会在不打开 Blob 文件的情况下，创建具有读/写特性的本地文件。

### <a name="to-upload-blobs"></a>上传 Blob
* 当容器处于打开状态，可以在 Blob 容器视图中查看时，选择“上传 Blob”按钮。
  
可以选择一个或多个文件进行上传，可以上传任何类型的文件。 “Azure 活动日志”窗口显示上传进度。 有关处理 Blob 数据的详细信息，请参阅[如何在 .NET 中使用 Azure Blob 存储](http://go.microsoft.com/fwlink/p/?LinkId=267911)。

### <a name="to-view-logs-transferred-to-blobs"></a>查看传输到 Blob 的日志
如果正在使用 Azure 诊断来记录 Azure 应用程序中的数据，并且已将日志传输到存储帐户，则会看到 Azure 为这些日志创建的容器。 在服务器资源管理器中查看这些日志是一种简便的确定应用程序问题的方法，尤其是当应用程序已部署到 Azure 时。 

有关 Azure 诊断的详细信息，请参阅 [Collect Logging Data by Using Azure Diagnostics](https://msdn.microsoft.com/library/azure/gg433048.aspx)（使用 Azure 诊断收集日志记录数据）。

### <a name="to-get-the-url-for-a-blob"></a>获取 Blob 的 URL
* 打开 Blob 的快捷菜单，并选择“复制 URL”。

### <a name="to-edit-a-blob"></a>编辑 Blob
* 选择 Blob，并选择“打开 Blob”按钮。
  
文件将下载到临时位置，在本地计算机上打开。 进行更改之后，必须再次上传 Blob。

## <a name="work-with-queue-resources"></a>处理队列资源
存储服务队列托管在 Azure 存储帐户中。 可以使用它们，允许云服务角色通过消息传递机制互相通信以及与其他服务通信。 可以用编程方式，通过云服务以及面向外部客户端的 Web 服务来访问队列。 还可以使用 Visual Studio 中的服务器资源管理器直接访问队列。

开发使用队列的云服务时，可能希望使用 Visual Studio 来创建队列，并在开发和测试代码时，以交互方式使用它们。

在服务器资源管理器中，可以查看存储帐户中的队列、创建和删除队列、打开队列以查看其消息，以及将消息添加到队列。 打开队列进行查看时，可以查看各条消息，还可使用左上角的按钮，对队列执行以下操作：

* 刷新队列的视图。
* 将消息添加到队列。
* 取消顶部消息的排队。
* 清除整个队列。

下图显示了包含两条消息的队列：

![查看队列](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

有关存储服务队列的详细信息，请参阅[通过 .NET 开始使用 Azure 队列存储](http://go.microsoft.com/fwlink/?LinkID=264702)。 有关存储服务队列的 Web 服务的详细信息，请参阅 [Queue Service Concepts](http://go.microsoft.com/fwlink/?LinkId=264788)（队列服务的概念）。 有关如何使用 Visual Studio 将消息发送到存储服务队列的信息，请参阅 [Sending Messages to a Storage Services Queue](https://msdn.microsoft.com/library/azure/jj649344.aspx)（将消息发送到存储服务队列）。

> [!NOTE]
> 存储服务队列不同于 Azure 服务总线队列。 有关服务总线队列的详细信息，请参阅[服务总线队列、主题和订阅](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-queues-topics-subscriptions)。
> 
> 

## <a name="work-with-table-resources"></a>处理表资源
Azure 表存储可存储大量结构化数据。 该服务是一个 NoSQL 数据存储，接受来自 Azure 云内部和外部的通过验证的呼叫。 Azure 表最适合存储结构化非关系型数据。

### <a name="to-create-a-table"></a>创建表
1. 在 Cloud Explorer 中，选择存储帐户的“表”节点，然后选择“创建表”。
2. 在“创建表”对话框中，输入表的名称。

### <a name="to-view-table-data"></a>查看表数据
1. 在云资源管理器中，打开“Azure”节点，然后打开“存储”节点。
2. 打开所需的存储帐户节点，并打开“表”节点以查看存储帐户的表列表。
3. 打开表的快捷菜单，并选择“查看表”。
   
    ![解决方案资源管理器中的 Azure 表](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

按实体（在行中所示）和属性（在列中所示）组织的表。 例如，下图显示了“表设计器”中列出的实体。

### <a name="to-edit-table-data"></a>编辑表数据
* 在“表设计器”中，打开实体（单行）或属性（单个单元格）的快捷菜单，并选择“编辑”。
   
    ![添加或编辑表实体](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)
   
单个表中不同实体不需要具有相同的属性集（列）。 在查看和编辑表数据时，请记住以下限制：
   
* 不能查看或编辑二进制数据 (`type byte[]`)，但可将它们存储在表中。
* 无法编辑 PartitionKey 或 RowKey 值，因为 Azure 中的表存储不支持该操作。
* 无法创建名为 Timestamp 的属性。 因为 Azure 存储服务使用了同名属性。
* 如果输入 DateTime 值，必须使用适合计算机区域和语言设置的格式（例如，对于美国英语，必须使用 MM/DD/YYYY HH:MM:SS [AM|PM]）。

### <a name="to-add-entities"></a>添加实体
1. 在“表设计器”中，选择“添加实体”按钮。
   
    ![“添加实体”按钮](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)
2. 在“添加实体”对话框中，输入 **PartitionKey** 和 **RowKey** 属性的值。
   
    ![“添加实体”对话框](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)
   
    输入值时应小心。 因为关闭此对话框后便无法更改它们，除非删除此实体，再次添加。

### <a name="to-filter-entities"></a>筛选实体
如果使用查询生成器，则可以自定义表中显示的实体集。

1. 若要打开查询生成器，请打开要查看的表。
2. 选择表视图工具栏上的“查询生成器”按钮。
   
    随后将出现“查询生成器”对话框。 下图显示了正在查询生成器中进行生成的查询。
   
    ![查询生成器](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
3. 查询生成完毕后，关闭该对话框。 所生成文本格式的查询以 WCF 数据服务筛选器的形式显示在文本框中。
4. 若要运行查询，请选择绿色三角形图标。
   
如果在筛选文本框中直接输入 WCF Data Services 筛选器字符串，还可以筛选“表设计器”中显示的实体数据。 此类字符串类似 SQL WHERE 语句，但它以 HTTP 请求的形式发送到服务器。 有关如何生成筛选器字符串的信息，请参阅 [Constructing Filter Strings for the Table Designer](https://msdn.microsoft.com/library/azure/ff683669.aspx)（生成表设计器的筛选字符串）。
   
下图显示了有效筛选字符串的示例：
   
![筛选器字符串](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## <a name="refresh-storage-data"></a>刷新存储数据
当服务器资源管理器连接到存储帐户或从存储帐户获取数据时，可能需要长达一分钟的时间才能完成操作。 如果服务器资源管理器无法连接，则操作可能会超时。在检索数据的同时，可以继续在 Visual Studio 的其他部分工作。 如果花费了太长时间想要取消操作，请选择服务器资源管理器工具栏上的“停止刷新”按钮。

### <a name="to-refresh-blob-container-data"></a>刷新 Blob 容器数据
* 选择“存储”下的“Blob”节点，并选择服务器资源管理器工具栏上的“刷新”按钮。
* 若要刷新显示的 Blob 列表，请选择“执行”按钮。

### <a name="to-refresh-table-data"></a>刷新表数据
* 选择“存储”下的“表”节点，并选择服务器资源管理器工具栏上的“刷新”按钮。
* 若要刷新“表设计器”中显示的实体列表，请在“表设计器”中选择“执行”按钮。

### <a name="to-refresh-queue-data"></a>刷新队列数据
* 选择“存储”下的“队列”节点，并选择服务器资源管理器工具栏上的“刷新”按钮。

### <a name="to-refresh-all-items-in-a-storage-account"></a>刷新存储帐户中的所有项
* 选择帐户名称，并选择服务器资源管理器工具栏上的“刷新”按钮。

## <a name="add-storage-accounts-by-using-server-explorer"></a>使用服务器资源管理器添加存储帐户
可以使用两种方法在服务器资源管理器中添加存储帐户。 可以在 Azure 订阅中创建存储帐户，也可以附加现有的存储帐户。

### <a name="to-create-a-storage-account-by-using-server-explorer"></a>使用服务器资源管理器创建存储帐户
1. 在服务器资源管理器中，打开“存储”节点的快捷菜单，并选择“创建存储帐户”。
   
2. 在“创建存储帐户”对话框中，选择或输入以下信息：
   
   * 要将存储帐户添加到的 Azure 订阅。
   * 要用于新存储帐户的名称。
   * 区域或地缘组（例如“美国西部”或“东亚”）。
   * 要用于存储帐户的复制类型，如本地冗余。

   ![创建 Azure 存储帐户](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

3. 选择“创建” 。
   
新的存储帐户随即出现在解决方案资源管理器的“存储”列表中。

### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>使用服务器资源管理器附加现有的存储帐户
1. 在服务器资源管理器中，打开 Azure“存储”节点的快捷菜单，并选择“附加外部存储”。
   
    ![添加现有的存储帐户](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
2. 在“创建存储帐户”对话框中，选择或输入以下信息：
   
   * 要附加的现有存储帐户的名称。
   * 所选存储帐户的密钥。 选择存储帐户时，通常会提供此值。 如果想要 Visual Studio 记住存储帐户密钥，请选中“记住帐户密钥”复选框。
   * 将用于连接到存储帐户的协议，如 HTTP、HTTPS 或自定义终结点。 有关自定义终结点的详细信息，请参阅[如何配置连接字符串](https://msdn.microsoft.com/library/azure/ee758697.aspx)。

### <a name="to-view-the-secondary-endpoints"></a>查看辅助终结点
如果使用“读取访问权限异地冗余”复制选项创建了存储帐户，可以查看该帐户的辅助终结点：

* 打开帐户名称的快捷菜单，并选择“属性”。
  
![存储辅助终结点](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>从服务器资源管理器中删除存储帐户
* 在服务器资源管理器中，打开帐户名称的快捷菜单，并选择“删除”。 

如果删除某个存储帐户，则也会删除该帐户的所有已保存密钥信息。
  
> [!NOTE]
> 从服务器资源管理器中删除存储帐户不会影响到存储帐户或其所包含的任何数据。 它只是将引用从服务器资源管理器中删除。 若要永久删除存储帐户，请使用 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)。
> 
> 

## <a name="next-steps"></a>后续步骤
若要详细了解如何使用 Azure 存储服务，请参阅[访问 Azure 存储服务](https://msdn.microsoft.com/library/azure/ee405490.aspx)。

