---
title: 将块 Blob 存储在设备上 - Azure IoT Edge |Microsoft Docs
description: 了解分层和生存时间功能，请参阅支持的 blob 存储操作并连接到 blob 存储帐户。
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a3eadb7a431d74a7a5f76c35b38c9d19fcfcf0d1
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987942"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>在 IoT Edge 上通过 Azure Blob 存储将数据存储在边缘

IoT Edge 上的 Azure Blob 存储在边缘提供了[块 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 存储解决方案。 IoT Edge 设备上的 blob 存储模块的行为类似于 Azure 块 blob 服务，不同之处在于块 blob 存储在本地 IoT Edge 设备上。 你可以使用相同的 Azure 存储 SDK 方法或已经习惯的块 blob API 调用来访问 blob。 本文介绍了在 IoT Edge 设备上运行 Blob 服务 IoT Edge 容器上与 Azure Blob 存储相关的概念。

此模块适用于方案：
* 需要将数据存储在本地，直到其可以处理或传输到云。 此数据可以是视频、图像、财务数据、医院数据或任何其他非结构化数据。
* 当设备位于具有有限连接性的位置时。
* 如果要在本地处理数据以获取对数据的低延迟访问，以便能够尽快响应突发。
* 当你想要降低带宽成本并避免将 tb 的数据传输到云时。 你可以在本地处理数据，并仅将已处理的数据发送到云。

观看视频以了解快速简介
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

此模块附带了**deviceToCloudUpload**和**deviceAutoDelete**功能。

**deviceToCloudUpload**是一种可配置的功能。 此函数自动将本地 blob 存储中的数据上载到 Azure，并且支持间歇性 internet 连接。 它使你能够：

- 打开/关闭 deviceToCloudUpload 功能。
- 选择将数据复制到 Azure 的顺序，如 NewestFirst 或 OldestFirst。
- 指定要将数据上传到的 Azure 存储帐户。
- 指定要上传到 Azure 的容器。 此模块允许您指定源容器和目标容器名称。
- 完成上传到云存储后，选择立即删除 blob 的功能
- 执行完整的 blob 上传（使用 `Put Blob` 操作）和块级别上传（使用 `Put Block` 和 `Put Block List` 操作）。

当 blob 由块组成时，此模块使用块级别上传。 下面是一些常见方案：

- 应用程序会更新以前上载的 blob 的某些块，此模块仅上传更新的块，而不是整个 blob。
- 此模块正在上传 blob，internet 连接消失，当连接再次返回时，它只会上传剩余的块而不是整个 blob。

如果在 blob 上传过程中发生意外的进程终止（如电源故障），则在模块重新联机后，将再次上载所有正在等待上载的块。

**deviceAutoDelete**是一种可配置的功能。 此函数在指定的持续时间（以分钟为单位）到期时，自动从本地存储中删除 blob。 它使你能够：

- 打开/关闭 deviceAutoDelete 功能。
- 指定将自动删除 blob 的时间（分钟（deleteAfterMinutes））。
- 选择在 deleteAfterMinutes 值过期的情况下上载 blob 的功能。


## <a name="prerequisites"></a>必备组件

Azure IoT Edge 设备：

- 可以按照适用于[Linux](quickstart-linux.md)或[Windows 设备](quickstart.md)的快速入门中的步骤，将你的开发计算机或虚拟机作为 IoT Edge 设备使用。

- 有关支持的操作系统和体系结构的列表，请参阅[Azure IoT Edge 支持的系统](support.md#operating-systems)。 IoT Edge 模块上的 Azure Blob 存储支持以下体系结构：
    - Windows AMD64
    - Linux AMD64
    - Linux ARM32
    - Linux ARM64 （预览版）

云资源：

Azure 中的标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload 和 deviceAutoDelete 属性

使用模块所需的属性设置**deviceToCloudUploadProperties**和**deviceAutoDeleteProperties**。 可以在部署期间设置所需的属性，也可以在以后通过编辑模块内的进行更改，无需重新部署。 建议为 `reported configuration` 和 `configurationValidation` 检查 "模块克隆"，以确保正确传播值。

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

此设置 `deviceToCloudUploadProperties`的名称。 如果使用 IoT Edge 模拟器，请将这些属性的值设置为相关的环境变量，可以在 "解释" 部分中找到这些属性。

| properties | 可能的值 | 说明 |
| ----- | ----- | ---- |
| uploadOn | true、false | 默认情况下，设置为 `false`。 如果要启用此功能，请将此字段设置为 `true`。 <br><br> 环境变量： `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst、OldestFirst | 允许你选择将数据复制到 Azure 的顺序。 默认情况下，设置为 `OldestFirst`。 此顺序由 Blob 的上次修改时间决定。 <br><br> 环境变量： `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` 是一个连接字符串，用于指定要将数据上传到的存储帐户。 指定 `Azure Storage Account Name`、`Azure Storage Account Key``End point suffix`。 添加适用于 Azure 的相应 EndpointSuffix，其中的数据将上载到，全局 Azure、政府 Azure 和 Microsoft Azure Stack 会有所不同。 <br><br> 可在此处选择指定 Azure 存储 SAS 连接字符串。 但必须在此属性过期时进行更新。 <br><br> 环境变量： `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | 允许你指定要上传到 Azure 的容器名称。 此模块允许您指定源容器和目标容器名称。 如果未指定目标容器名称，则会自动将容器名称分配为 `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`。 你可以为 "目标容器名称" 创建模板字符串，然后查看 "可能的值" 列。 <br>*% h-> IoT 中心名称（3-50 个字符）。 <br>*% d-> IoT Edge 设备 ID （1到129个字符）。 <br>*% m-> 模块名称（1到64个字符）。 <br>*% c-> 源容器名称（3到63个字符）。 <br><br>容器名称的最大大小为63个字符，而当容器大小超过63个字符时自动分配目标容器名称，它会将每个部分（IoTHubName、IotEdgeDeviceID、ModuleName、SourceContainerName）剪裁到15符. <br><br> 环境变量： `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | true、false | 默认情况下，设置为 `false`。 如果设置为 `true`，它将在上传到云存储完成后自动删除数据。 <br><br> 环境变量： `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

此设置 `deviceAutoDeleteProperties`的名称。 如果使用 IoT Edge 模拟器，请将这些属性的值设置为相关的环境变量，可以在 "解释" 部分中找到这些属性。

| properties | 可能的值 | 说明 |
| ----- | ----- | ---- |
| deleteOn | true、false | 默认情况下，设置为 `false`。 如果要启用此功能，请将此字段设置为 `true`。 <br><br> 环境变量： `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | 指定以分钟为单位的时间。 此模块将在此值过期时自动从本地存储中删除 blob。 <br><br> 环境变量： `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true、false | 默认情况下，它设置为 `true`，在 deleteAfterMinutes 过期时，它将在上传到云存储时保留 blob。 可以将其设置为 `false`，它将在 deleteAfterMinutes 过期后立即删除数据。 注意：若要使用此属性，应将 uploadOn 设置为 true。 <br><br> 环境变量： `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>使用 SMB 共享作为本地存储
在 Windows 主机上部署此模块的 Windows 容器时，可以提供 SMB 共享作为本地存储路径。

请确保 SMB 共享和 IoT 设备位于相互信任的域中。

可以在运行 Windows 的 IoT 设备上运行 `New-SmbGlobalMapping` PowerShell 命令来映射 SMB 共享。

下面是配置步骤：
```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```
示例： <br>
`$creds = Get-Credential` <br>
`New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G: `

此命令将使用凭据向远程 SMB 服务器进行身份验证。 然后，将远程共享路径映射到 G：驱动器号（可以是任何其他可用的驱动器号）。 IoT 设备现在已将数据卷映射到 G：驱动器上的路径。 

请确保 IoT 设备中的用户可以读取/写入远程 SMB 共享。

对于你的部署，`<storage mount>` 的值可以是**G：/ContainerData： C：/BlobRoot**。 

## <a name="granting-directory-access-to-container-user-on-linux"></a>授予对 Linux 上的容器用户的目录访问权限
如果在 Linux 容器的 "创建" 选项中使用了 "用于存储的[卷装入](https://docs.docker.com/storage/volumes/)"，则无需执行任何额外的步骤，但如果使用了[bind 装载](https://docs.docker.com/storage/bind-mounts/)，则需要执行这些步骤才能正确运行该服务。

按照最低权限原则将用户的访问权限限制为用户执行其工作所需的最低权限，此模块包括用户（名称： absie、ID：11000）和用户组（name： absie，ID：11000）。 如果容器以**root**身份启动（默认用户为**root**用户），则我们的服务将作为低特权**absie**用户启动。 

此行为会使主机路径的权限配置对服务的访问权限进行正确的绑定，否则服务将崩溃，并出现拒绝访问错误。 目录绑定中使用的路径需要容器用户可访问（示例： absie 11000）。 可以通过在主机上执行以下命令来授予容器用户对目录的访问权限：

```terminal
sudo chown -R 11000:11000 <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

示例：<br>
`sudo chown -R 11000:11000 /srv/containerdata` <br>
`sudo chmod -R 700 /srv/containerdata `


如果需要以非**absie**的用户身份运行服务，则可以在部署清单中的 "user" 属性下的 createOptions 中指定自定义用户 ID。 在这种情况下，需要使用默认或根组 ID `0`。

```json
“createOptions”: { 
  “User”: “<custom user ID>:0” 
} 
```
现在，授予容器用户对目录的访问权限
```terminal
sudo chown -R <user ID>:<group ID> <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

## <a name="configure-log-files"></a>配置日志文件

有关为模块配置日志文件的信息，请参阅这些[生产最佳实践](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)。

## <a name="connect-to-your-blob-storage-module"></a>连接到 blob 存储模块

可以使用为模块配置的帐户名和帐户密钥来访问 IoT Edge 设备上的 blob 存储。

将你的 IoT Edge 设备指定为对其进行的任何存储请求的 blob 终结点。 你可以使用 IoT Edge 设备信息和配置的帐户名[为显式存储终结点创建连接字符串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。

- 对于部署在 IoT Edge 模块上的 Azure Blob 存储所在的同一设备上的模块，blob 端点为： `http://<module name>:11002/<account name>`。
- 对于在其他设备上运行的模块或应用程序，您必须为您的网络选择正确的终结点。 根据您的网络设置，选择一个终结点格式，以便来自外部模块或应用程序的数据流量可以访问运行 IoT Edge 模块上的 Azure Blob 存储的设备。 此方案的 blob 终结点是以下其中之一：
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 存储快速入门示例

Azure Blob 存储文档包含多种语言的快速入门示例代码。 若要运行这些示例，请通过更改 Blob 终结点以连接到本地 Blob 存储模块来测试 IoT Edge 上的 Azure Blob 存储。

以下快速入门示例使用 IoT Edge 也支持的语言，因此你可以将它们作为 IoT Edge 模块一起部署到 blob 存储模块：

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
    - 使用此 SDK 时遇到已知问题，因为此版本的模块不会返回 blob 创建时间。 因此，很少有列表 blob 等方法不起作用。 解决方法：在 blob 客户端上将 API 版本显式设置为 "2017-04-17"。 <br>示例：`block_blob_service._X_MS_VERSION = '2017-04-17'`
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-v10.md)
- [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-v10.md)
- [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
- [Go](../storage/blobs/storage-quickstart-blobs-go.md)
- [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>通过 Azure 存储资源管理器连接到本地存储

您可以使用[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)连接到您的本地存储帐户。

1. 下载并安装 Azure 存储资源管理器

1. 使用连接字符串连接到 Azure 存储

1. 提供连接字符串： `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 完成连接的步骤。

1. 在本地存储帐户中创建容器

1. 开始将文件上传为块 blob。
   > [!NOTE]
   > 此模块不支持页 blob。

1. 也可以选择将 Azure 存储帐户连接存储资源管理器。 此配置为你提供本地存储帐户和 Azure 存储帐户的单一视图

## <a name="supported-storage-operations"></a>受支持的存储操作

IoT Edge 上的 Blob 存储模块使用 Azure 存储 Sdk，并且与适用于块 blob 终结点的2017-04-17 版本的 Azure 存储 API 一致。 

由于并非所有 Azure Blob 存储操作都支持 IoT Edge 上的 Azure Blob 存储，此部分列出了每个操作的状态。

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
- 列出 Blob
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

## <a name="event-grid-on-iot-edge-integration"></a>IoT Edge 集成的事件网格
> [!CAUTION]
> IoT Edge 上的与事件网格的集成处于预览阶段

IoT Edge 模块上的此 Azure Blob 存储现在可在 IoT Edge 上提供与事件网格的集成。 有关此集成的详细信息，请参阅[教程：部署模块、发布事件和验证事件传递](../event-grid/edge/react-blob-storage-events-locally.md)。

## <a name="release-notes"></a>发行说明

下面是此模块的[docker hub 中的发行说明](https://hub.docker.com/_/microsoft-azure-blob-storage)

## <a name="feedback"></a>反馈

你的反馈对我们非常重要，因为我们可以使此模块及其功能非常有用且易于使用。 请分享你的反馈，让我们了解我们如何改进。

你可以通过 absiotfeedback@microsoft.com 联系

## <a name="next-steps"></a>后续步骤

了解如何[在 IoT Edge 上部署 Azure Blob 存储](how-to-deploy-blob.md)

在[IoT Edge 博客上的 Azure Blob 存储](https://aka.ms/abs-iot-blogpost)中随时了解最新的更新和公告
