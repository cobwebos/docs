---
title: 将块 Blob 存储在设备上 - Azure IoT Edge |Microsoft Docs
description: 了解分层和生存时间的功能，请参阅受支持的 blob 存储操作，和连接到 blob 存储帐户。
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 396af2dfd9fc53c080163a27e376328c1369d5e1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991470"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>通过 IoT Edge 上的 Azure Blob 存储（预览版）在边缘存储数据

IoT Edge 上的 Azure Blob 存储在边缘提供了[块 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 存储解决方案。 IoT Edge 设备上的 blob 存储模块的行为类似于 Azure 块 blob 服务，但块 blob 存储在本地 IoT Edge 设备上。 你可以使用相同的 Azure 存储 SDK 方法或已经习惯的块 blob API 调用来访问 blob。

> [!NOTE]
> IoT Edge 上的 Azure Blob 存储现为[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此模块附带**分层**并**生存时间**功能。

> [!NOTE]
> 当前分层和生存时间的功能仅在 Linux AMD64 和 Linux ARM32 中可用。

**分层**是可配置的功能，可用于自动上传到 Azure 的间歇性的 internet 连接支持从本地 blob 存储数据。 该功能允许：

- 启用/禁用分层功能
- 选择在其中将数据复制到 Azure NewestFirst 或 OldestFirst 等的顺序
- 指定要将数据上传到的 Azure 存储帐户。
- 指定要上传到 Azure 的容器。 此模块允许指定源和目标容器名称。
- 执行完整 Blob 分层（使用 `Put Blob` 操作）和块级分层（使用 `Put Block` 和 `Put Block List` 操作）。

如果 Blob 由块构成，则此模块使用块级分层。 下面是一些常见场景：

- 你的应用程序更新一些以前上传 blob 的块，此模块将只更新后的块和不完整的 blob 上传。
- 该模块将上传 blob 和 internet 连接将消失，建立连接时返回再次上传仅剩余的块和不完整的 blob。

如果在 Blob 上传期间发生意外的进程终止（例如电源故障），当模块重新联机时，将再次上传需要上传的所有块。

**生存时间**(TTL) 是可配置的功能，该模块会自动删除 blob 从本地存储时指定的持续时间 （以分钟计） 过期。 TTL 可以：

- 启用/禁用分层功能
- 以分钟为单位指定 TTL

对于需要在本地存储视频、图像、金融数据、医院数据或其他任何数据，以后在本地处理数据或将其传输到云的场合，都很适合使用此模块。

本文提供有关在 IoT Edge 设备上运行 blob 服务的 IoT Edge 容器中部署 Azure Blob 存储的说明。

> [!NOTE]
> "自动终止"视频中所用的"自动分层"的条款已替换为"分层"和"--生存时间"。

观看视频，快速了解
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>必备组件

Azure IoT Edge 设备：

- 你可以通过在开发计算机或虚拟机为 IoT Edge 设备的快速入门中的步骤[Linux](quickstart-linux.md)或[Windows 设备](quickstart.md)。

- IoT Edge 模块上的 Azure Blob 存储支持以下设备配置：

  | 操作系统 | 体系结构 |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT 核心版（10 月更新） | AMD64 |
  | Windows 10 IoT 企业版（10 月更新） | AMD64 |
  | Windows Server 2019 | AMD64 |
  | Raspbian-stretch | ARM32 |

云资源：

Azure 中的标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="tiering-and-time-to-live-properties"></a>分层和生存时间属性

使用所需的属性来设置分层和生存时间属性。 可以在部署期间设置这些属性，或者，以后可以通过编辑模块孪生来更改这些属性，而无需重新部署。 我们建议检查“模块孪生”中的 `reported configuration` 和 `configurationValidation`，以确保正确传播值。

### <a name="tiering-properties"></a>分层属性

此设置的名称为 `tieringSettings`

| 字段 | 可能的值 | 说明 |
| ----- | ----- | ---- |
| tieringOn | true、false | 默认设置为 `false`，若要启用它，可将它设置为 `true`|
| backlogPolicy | NewestFirst、OldestFirst | 可以选择在其中将数据复制到 Azure 的顺序。 默认设置为 `OldestFirst`。 顺序由 Blob 的上次修改时间确定 |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` 是一个连接字符串，用于指定要将数据上传到的 Azure 存储帐户。 指定 `Azure Storage Account Name`、`Azure Storage Account Key` 或 `End point suffix`。 添加相应 EndpointSuffix 的 Azure 位置将上传数据，它对于各不相同全局 Azure，政府 Azure 和 Microsoft Azure Stack。 |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | 用于指定要上传到 Azure 的容器名称。 此模块允许指定源和目标容器名称。 如果未指定目标容器名称，系统会自动分配 `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>` 作为容器名称。 您可以创建目标容器的名称的模板字符串，请查看可能的值列。 <br>* %h -> IoT 中心名称（3 到 50 个字符）。 <br>* %d-> IoT 设备 ID (1 到 129 个字符)。 <br>* %m -> 模块名称（1 到 64 个字符）。 <br>* %c -> 源容器名称（3 到 63 个字符）。 <br><br>容器名称的最大大小为 63 个字符。尽管系统会自动分配目标容器名称，但如果容器大小超过 63 个字符，系统会将每个部分（IoTHubName、IotEdgeDeviceName、ModuleName、ContainerName）修剪为 15 个字符。 |

### <a name="time-to-live-properties"></a>生存时间属性

此设置的名称为 `ttlSettings`

| 字段 | 可能的值 | 说明 |
| ----- | ----- | ---- |
| ttlOn | true、false | 默认设置为 `false`，若要启用它，可将它设置为 `true`|
| timeToLiveInMinutes | `<minutes>` | 以分钟为单位指定 TTL。 当本地存储中的 Blob 达到 TTL 时，模块会自动删除这些 Blob |

## <a name="configure-log-files"></a>配置日志文件

有关配置你的模块的日志文件的信息，请参阅这些[生产的最佳做法](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)。

## <a name="connect-to-your-blob-storage-module"></a>连接到 blob 存储模块

可以使用为模块配置的帐户名和帐户密钥来访问 IoT Edge 设备上的 blob 存储。

将你的 IoT Edge 设备指定为对其进行的任何存储请求的 blob 终结点。 你可以使用 IoT Edge 设备信息和配置的帐户名[为显式存储终结点创建连接字符串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。

- 对于作为 IoT Edge 模块上的 Azure Blob 存储在何处运行在同一设备部署的模块，blob 终结点是： `http://<module name>:11002/<account name>`。
- 对于部署在设备上 IoT Edge 模块的 Azure Blob 存储是运行，则取决于你的设置 blob 位置之外的不同设备的模块终结点是之一：
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 存储快速入门示例

Azure Blob 存储文档包括以多种语言提供示例代码的快速入门。 你可以运行这些示例以测试通过更改要连接到 blob 存储模块的 blob 终结点的 IoT Edge 上的 Azure Blob 存储。

以下快速入门使用 IoT Edge 也同样支持的语言，因此，你可以将它们作为 IoT Edge 模块与 blob 存储模块一起部署：

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>连接到本地存储与 Azure 存储资源管理器

可以使用**Azure 存储资源管理器**连接到本地存储帐户。 这是仅可用于[Azure 存储资源管理器版本 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0)。

> [!NOTE]
> 执行以下步骤，例如将连接添加到本地存储帐户，或在本地存储帐户中创建容器时，可能会遇到错误。 请忽略并刷新。

1. 下载并安装 Azure 存储资源管理器

1. 连接到 Azure 存储中使用的连接字符串

1. 提供连接字符串： `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 经过连接的步骤。

1. 创建本地存储帐户内的容器

1. 开始上传文件作为块 blob。
   > [!NOTE]
   > 此模块不支持页 blob。

1. 您可以选择要上传数据连接你的 Azure 存储帐户。 它提供单个视图对本地存储帐户和 Azure 存储帐户

## <a name="supported-storage-operations"></a>受支持的存储操作

IoT Edge 上的 Blob 存储模块使用相同的 Azure 存储 SDK，并与适用于块 Blob 终结点的 2017-04-17 版 Azure 存储 API 保持一致。 后期版本取决于客户需求。

由于并非所有的 Azure Blob 存储操作针对 IoT Edge 的 Azure Blob 存储支持，此部分列出了每个状态。

### <a name="account"></a>帐户

受支持：

- 列出容器

不受支持：

- 获取和设置 blob 服务属性
- 预检 blob 请求
- 获取 blob 服务统计信息
- 获取帐户信息

### <a name="containers"></a>容器

受支持：

- 创建和删除容器
- 获取容器属性和元数据
- 列出 blob
- 获取和设置容器 ACL
- 设置容器元数据

不受支持：

- 租用容器

### <a name="blobs"></a>Blob

受支持：

- 放置、获取和删除 blob
- 获取和设置 blob 属性
- 获取和设置 blob 元数据

不受支持：

- 租用 blob
- 获取 blob 快照
- 复制和中止复制 blob
- 撤销删除 blob
- 设置 blob 层

### <a name="block-blobs"></a>块 Blob

受支持：

- 放置块
- 放置和获取块列表

不受支持：

- 放置来自 URL 的块

## <a name="feedback"></a>反馈

你的反馈对我们很重要以使此模块和其功能非常有用且易于使用。 请分享你的反馈并告诉我们该如何改进。

你可以联系我们 absiotfeedback@microsoft.com

## <a name="next-steps"></a>后续步骤

详细了解[IoT Edge 上部署 Azure Blob 存储](how-to-deploy-blob.md)
