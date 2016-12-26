---
title: "通过 DocumentDB 模拟器在本地开发 | Microsoft Docs"
description: "利用 Azure DocumentDB 模拟器，可以在本地免费开发和测试应用程序，无需创建 Azure 订阅。"
services: documentdb
documentationcenter: 
keywords: "DocumentDB 模拟器"
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 0d0264857f552b65f5934eea686329e8d36a1204
ms.openlocfilehash: e86b1990ea38f9f8af4e5401fe37eec321075e64


---
# <a name="use-the-azure-documentdb-emulator-for-development-and-testing"></a>将 Azure DocumentDB 模拟器用于开发和测试

[**下载模拟器**](https://aka.ms/documentdb-emulator)

为方便进行开发，Azure DocumentDB 模拟器提供了一个模拟 Azure DocumentDB 服务的本地环境。 利用 DocumentDB 模拟器，可在本地开发和测试应用程序，无需创建 Azure 订阅且不会产生任何费用。 如果对应用程序在 DocumentDB Emulator 中的工作情况感到满意，可以改为在云中使用 Azure DocumentDB 帐户。

我们建议通过观看以下视频来入门，Kirill Gavrylyuk 在视频中演示了如何开始使用 DocumentDB 模拟器。

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="documentdb-emulator-system-requirements"></a>DocumentDB 模拟器系统要求
DocumentDB 模拟器具有以下硬件和软件要求：

* 软件要求
  * Windows Server 2012 R2、Windows Server 2016 或 Windows 10
*   最低硬件要求
  * 2 GB RAM
  * 10 GB 可用硬盘空间

## <a name="installing-the-documentdb-emulator"></a>安装 DocumentDB 模拟器
可以从 [Microsoft 下载中心](https://aka.ms/documentdb-emulator)下载并安装 DocumentDB 模拟器。 

> [!NOTE]
> 若要安装、配置和运行 DocumentDB 模拟器，必须在计算机上具有管理权限。

## <a name="checking-for-documentdb-emulator-updates"></a>检查 DocumentDB 模拟器更新
DocumentDB 模拟器包括一个内置 Azure DocumentDB 数据资源管理器，用于浏览存储在 DocumentDB 中的数据、创建新集合，并在新的更新可供下载时通知用户。 

> [!NOTE]
> 在某版本的 DocumentDB 模拟器中创建数据后，不能保证在使用其他版本时可访问。 如果需要长期保存数据，建议将该数据存储在 Azure DocumentDB 帐户中，而不是存储在 DocumentDB 模拟器中。 

## <a name="how-the-documentdb-emulator-works"></a>DocumentDB 模拟器的工作原理
DocumentDB 模拟器提供对 DocumentDB 服务的高保真模拟。 它支持与 Azure DocumentDB 相同的功能，包括创建和查询 JSON 文档，配置和扩展集合，以及执行存储过程和触发器。 可以使用 DocumentDB 模拟器开发和测试应用程序，并通过对 DocumentDB 的连接终结点进行单一配置更改将其部署到全局范围的 Azure。

虽然已创建实际 DocumentDB 服务的高保真本地模拟，但 DocumentDB 模拟器的实现不同于该服务。 例如，DocumentDB 模拟器使用标准 OS 组件，例如用于暂留的本地文件系统和用于连接的 HTTPS 协议堆栈。 这意味着，不可通过 DocumentDB 模拟器使用某些依赖于 Azure 基础结构的功能，如全局复制、读/写的单位数毫秒延迟，以及可调整的一致性级别。


## <a name="authenticating-requests-against-the-documentdb-emulator"></a>针对 DocumentDB 模拟器，对请求进行身份验证
与云中的 Azure 文档一样，针对 DocumentDB 模拟器的每个请求都必须进行身份验证。 DocumentDB 模拟器支持单一固定帐户和用于主密钥身份验证的公开的身份验证密钥。 此帐户和密钥是允许用于 DocumentDB 模拟器的唯一凭据。 它们具有以下特点：

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> DocumentDB 模拟器支持的主密钥仅可用于模拟器。 不能在 DocumentDB 模拟器中使用生产 DocumentDB 帐户和密钥。 

此外，与 Azure DocumentDB 服务一样，DocumentDB 模拟器仅支持采用 SSL 的安全通信。

## <a name="start-and-initialize-the-documentdb-emulator"></a>启动和初始化 DocumentDB 模拟器

若要启动 Azure DocumentDB 模拟器，请选择“启动”按钮或按 Windows 键。 开始键入**DocumentDB 模拟器**，然后从应用程序列表中选择该模拟器。 

![选择“启动”按钮或按 Windows 键，开始键入“DocumentDB 模拟器”，然后从应用程序列表中选择该模拟器](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-start.png)

运行模拟器时，在 Windows 任务栏通知区域中会显示一个图标。 默认情况下 DocumentDB 模拟器在本地计算机（“localhost”）上运行，侦听端口 8081。

![DocumentDB 本地模拟器任务栏通知](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-taskbar.png)

DocumentDB 模拟器默认安装到 `C:\Program Files\DocumentDB Emulator` 目录。 还可以从命令行启动和停止该模拟器。 有关详细信息，请参阅[命令行工具参考](#command-line)。

## <a name="start-the-local-emulator-data-explorer"></a>启动本地模拟器的数据资源管理器

本地模拟器启动时，将在浏览器中自动打开 DocumentDB 数据资源管理器。 地址将显示为 [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html)。 如果关闭浏览器后要重新打开该数据资源管理器，可以在浏览器中打开该 URL 或通过 Windows 任务栏图标中的 DocumentDB 模拟器启动，如下所示。

![DocumentDB 本地模拟器数据资源管理器启动器](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-data-explorer-launcher.png)

## <a name="developing-with-the-documentdb-emulator"></a>通过 DocumentDB 模拟器进行开发
在桌面上运行 DocumentDB 模拟器以后，可以使用任何支持的 [DocumentDB SDK](documentdb-sdk-dotnet.md) 或 [DocumentDB REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 与模拟器进行交互。 DocumentDB 模拟器还包括内置数据资源管理器，可以利用它在不编写任何代码的情况下创建集合、查看和编辑文档。 

    // Connect to the DocumentDB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==",
        new ConnectionPolicy { EnableEndpointDiscovery = false });

> [!NOTE]
> 连接到模拟器时，必须在连接配置中设置为 EnableEndpointDiscovery = false。

如果使用 [DocumentDB 的 MongoDB 协议支持](documentdb-protocol-mongodb.md)，请使用以下连接字符串：

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10250/admin?ssl=true&3t.sslSelfSignedCerts=true

可以通过现有工具连接到 DocumentDB 模拟器，例如 [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio)。 还可以通过 [DocumentDB 数据迁移工具](https://github.com/azure/azure-documentdb-datamigrationtool)在 DocumentDB 模拟器和 Azure DocumentDB 服务之间迁移数据。

## <a name="export-the-documentdb-emulator-ssl-certificate"></a>导出 DocumentDB 模拟器 SSL 证书

.Net 语言和运行时通过 Windows 证书存储安全地连接到 DocumentDB 本地模拟器。 其他语言有自己管理和使用证书方法。 Java 使用自己的[证书存储](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)，而 Python 使用[套接字包装器](https://docs.python.org/2/library/ssl.html)。

为了获得用于语言和运行时（未与 Windows 证书存储集成）的证书，需要通过 Windows 证书管理器将其导出。 可以通过运行 certlm.msc 开始操作，也可以按照“[导出 DocumentDB 模拟器证书](./documentdb-nosql-local-emulator-export-ssl-certificates.md)”一文中的分步说明操作。 证书管理器开始运行后，打开个人证书（如下所示），并将友好名称为“DocumentDBEmulatorCertificate”的证书导出为 BASE-64 编码的 X.509 (.cer) 文件。

![DocumentDB 本地模拟器 SSL 证书](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-ssl_certificate.png)

按照“[Adding a Certificate to the Java CA Certificates Store](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store)”（将证书添加到 Java CA 证书存储）一文中的说明可将 X.509 证书导入 Java 证书存储中。  证书导入 cacerts 存储后，Java 和 MongoDB 应用程序将能够连接到 DocumentDB 本地模拟器。

## <a name="a-idcommand-lineadocumentdb-emulator-command-line-tool-reference"></a><a id="command-line"></a>DocumentDB 模拟器命令行工具参考
从安装位置中，可以使用命令行启动和停止模拟器、配置选项和执行其他操作。

### <a name="command-line-syntax"></a>命令行语法

    DocumentDB.LocalEmulator.exe [/shutdown] [/datapath] [/port] [/mongoport] [/directports] [/key] [/?]

若要查看选项列表，请在命令提示符下键入 `DocumentDB.LocalEmulator.exe /?`。

<table>
<tr>
  <td><strong>选项</strong></td>
  <td><strong>说明</strong></td>
  <td><strong>命令</strong></td>
  <td><strong>参数</strong></td>
</tr>
<tr>
  <td>[无参数]</td>
  <td>使用默认设置启动 DocumentDB 模拟器</td>
  <td>DocumentDB.LocalEmulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>关机</td>
  <td>关闭 DocumentDB 模拟器</td>
  <td>DocumentDB.LocalEmulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>帮助</td>
  <td>显示命令行参数的列表</td>
  <td>DocumentDB.LocalEmulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>数据路径</td>
  <td>指定要在其中存储数据文件的路径</td>
  <td>DocumentDB.LocalEmulator.exe /datapath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;：可访问的路径</td>
</tr>
<tr>
  <td>端口</td>
  <td>指定用于模拟器的端口号。  默认值为 8081</td>
  <td>DocumentDB.LocalEmulator.exe /port=&lt;port&gt;</td>
  <td>&lt;port&gt;：单个端口号</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>指定用于 MongoDB 兼容性 API 的端口号。 默认值为 10250</td>
  <td>DocumentDB.LocalEmulator.exe /mongoport=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;：单个端口号</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>指定用于直接连接的端口。 默认值为 10251、10252、10253、10254</td>
  <td>DocumentDB.LocalEmulator.exe /directports:&lt;directports&gt;</td>
  <td>&lt;directports&gt;：以逗号分隔的 4 个端口的列表</td>
</tr>
<tr>
  <td>键</td>
  <td>模拟器的授权密钥。 密钥必须是 64 字节向量的 base 64 编码</td>
  <td>DocumentDB.LocalEmulator.exe /key:&lt;key&gt;</td>
  <td>&lt;key&gt;：密钥必须是 64 字节向量的 base 64 编码</td>
</tr>
<tr>
  <td>EnableThrottling</td>
  <td>指定启用了请求限制行为</td>
  <td>DocumentDB.LocalEmulator.exe /enablethrottling</td>
  <td></td>
</tr>
<tr>
  <td>DisableThrottling</td>
  <td>指定禁用了请求限制行为</td>
  <td>DocumentDB.LocalEmulator.exe /disablethrottling</td>
  <td></td>
</tr>
</table>

## <a name="differences-between-the-documentdb-emulator-and-azure-documentdb"></a>DocumentDB 模拟器与 Azure DocumentDB 之间的差异 
由于 DocumentDB 模拟器提供在本地开发人员工作站上运行的模拟环境，因此模拟器与云中的 Azure DocumentDB 帐户之间在功能上存在一些差异：

* DocumentDB 模拟器只支持单一固定帐户和公开的主密钥。  在 DocumentDB 模拟器中无法重新生成密钥。
* DocumentDB 模拟器不是可缩放的服务，并且不支持大量的集合。
* DocumentDB 模拟器不模拟不同的 [DocumentDB 一致性级别](documentdb-consistency-levels.md)。
* DocumentDB 模拟器不模拟[多区域复制](documentdb-distribute-data-globally.md)。
* DocumentDB 模拟器不支持服务配额替代，而 Azure DocumentDB 服务支持（例如文档大小限制、增加的分区集合存储）。
* 由于 DocumentDB 模拟器副本不一定能反映 Azure DocumentDB 服务的最新更改，因此请使用 [DocumentDB 容量规划器](https://www.documentdb.com/capacityplanner)准确估计应用程序的生产吞吐量 (RU) 需求。

## <a name="next-steps"></a>后续步骤
* 若要了解有关 DocumentDB 的详细信息，请参阅 [Azure DocumentDB 简介](documentdb-introduction.md)
* 若要开始使用 DocumentDB 模拟器进行开发，请下载一个[支持的 DocumentDB SDK](documentdb-sdk-dotnet.md)。



<!--HONumber=Nov16_HO5-->


