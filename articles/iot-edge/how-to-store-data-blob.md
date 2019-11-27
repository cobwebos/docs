---
title: 将块 Blob 存储在设备上 - Azure IoT Edge |Microsoft Docs
description: 了解分层和生存时间功能、查看支持的 Blob 存储操作，以及连接到 Blob 存储帐户。
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0dd999d7c7e42f4b881465c8473e0069952561ba
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456770"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>使用 IoT Edge 上的 Azure Blob 存储在边缘中存储数据

IoT Edge 上的 Azure Blob 存储在边缘提供了[块 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 存储解决方案。 IoT Edge 设备上的 Blob 存储模块的行为类似于 Azure 块 blob 服务，但块 Blob 存储在本地 IoT Edge 设备上。 你可以使用相同的 Azure 存储 SDK 方法或已经习惯的块 blob API 调用来访问 blob。 本文说明与 IoT Edge 容器中的 Azure Blob 存储相关的概念，该容器在 IoT Edge 设备上运行 Blob 服务。

此模块适用于以下方案：
* 在处理数据或将其传输到云中之前，需在本地存储数据。 这些数据可以是视频、图像、财务数据、医疗数据或者任何其他非结构化数据。
* 设备位于连接受限的位置。
* 需要在本地有效处理数据以便以较低的延迟访问数据，并尽快地对紧急情况做出反应。
* 需要降低带宽成本，避免将 TB 量级的数据传输到云中。 可在本地处理数据，并仅将已处理的数据发送到云中。

观看视频以了解快速简介
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

此模块附带 **deviceToCloudUpload** 和 **deviceAutoDelete** 功能。

**deviceToCloudUpload** 是一个可配置的功能。 此函数可将本地 Blob 存储中的数据自动上传到 Azure，并支持间歇性的 Internet 连接。 该功能允许：

- 启用/禁用 deviceToCloudUpload 功能。
- 选择将数据复制到 Azure 的顺序，例如，NewestFirst 或 OldestFirst。
- 指定要将数据上传到的 Azure 存储帐户。
- 指定要上传到 Azure 的容器。 此模块允许指定源和目标容器名称。
- 选择将内容上传到云存储后立即删除 Blob 的功能
- 执行完整 Blob 上传（使用 `Put Blob` 操作）和块级上传（使用 `Put Block` 和 `Put Block List` 操作）。

如果 Blob 由块构成，则此模块使用块级上传。 下面是一些常见场景：

- 应用程序需要更新以前上传的 Blob 的某些块；此模块只上传更新的块，而不上传整个 Blob。
- 当模块正在上传 Blob 时，Internet 连接断开；连接恢复后，该模块只上传剩余的块，而不上传整个 Blob。

如果在 Blob 上传期间发生意外的进程终止（例如电源故障），当模块重新联机后，将再次上传需要上传的所有块。

**deviceAutoDelete** 是一个可配置的功能。 超过指定的持续时间（度量单位为分钟）时，此函数将自动从本地存储中删除 Blob。 该功能允许：

- 启用/禁用 deviceAutoDelete 功能。
- 指定以分钟为单位的时间 (deleteAfterMinutes)，该时间过后会自动删除这些 Blob。
- 选择在 deleteAfterMinutes 值到期后保留上传的 Blob 的功能。


## <a name="prerequisites"></a>先决条件

Azure IoT Edge 设备：

- 可以按照适用于 [Linux](quickstart-linux.md) 或 [Windows 设备](quickstart.md)的快速入门中的步骤，将开发计算机或虚拟机用作 IoT Edge 设备。

- 有关支持的操作系统和体系结构的列表，请参阅 [Azure IoT Edge 支持的系统](support.md#operating-systems)。 IoT Edge 模块上的 Azure Blob 存储支持以下体系结构：
    - Windows AMD64
    - Linux AMD64
    - Linux ARM32
    - Linux ARM64（预览版）

云资源：

Azure 中的标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload 和 deviceAutoDelete 属性

使用模块的所需属性设置 **deviceToCloudUploadProperties** 和 **deviceAutoDeleteProperties**。 可以在部署期间设置所需属性，或者，以后可以通过编辑模块孪生来更改这些属性，而无需重新部署。 我们建议检查“模块孪生”中的 `reported configuration` 和 `configurationValidation`，以确保正确传播值。

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

此设置的名称为 `deviceToCloudUploadProperties`。 如果使用 IoT Edge 模拟器，请将这些属性的值设置为相关环境变量，可以在说明部分中找到它们。

| 属性 | 可能的值 | 说明 |
| ----- | ----- | ---- |
| uploadOn | true、false | 默认设置为 `false`。 若要启用此功能，请将此字段设置为 `true`。 <br><br> 环境变量：`deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst、OldestFirst | 用于选择将数据复制到 Azure 的顺序。 默认设置为 `OldestFirst`。 顺序由 Blob 的上次修改时间确定。 <br><br> 环境变量：`deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` 是一个连接字符串，用于指定要将数据上传到的存储帐户。 指定 `Azure Storage Account Name`、`Azure Storage Account Key` 或 `End point suffix`。 添加用于上传数据的适当 Azure EndpointSuffix，它在全局 Azure、政府 Azure 和 Microsoft Azure Stack 中是不同的。 <br><br> 可以选择在此处选择指定 Azure 存储 SAS 连接字符串。 但是，在此属性过期时必须将其更新。 <br><br> 环境变量：`deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | 用于指定要上传到 Azure 的容器名称。 此模块允许指定源和目标容器名称。 如果未指定目标容器名称，系统会自动分配 `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>` 作为容器名称。 可以创建目标容器名称的模板字符串，具体请查看“可能的值”列。 <br>* %h -> IoT 中心名称（3 到 50 个字符）。 <br>* %d -> IoT Edge 设备 ID（1 到 129 个字符）。 <br>* %m -> 模块名称（1 到 64 个字符）。 <br>* %c -> 源容器名称（3 到 63 个字符）。 <br><br>容器名称的最大大小为 63 个字符。尽管系统会自动分配目标容器名称，但如果容器大小超过 63 个字符，系统会将每个部分（IoTHubName、IotEdgeDeviceID、ModuleName、SourceContainerName）修剪为 15 个字符。 <br><br> 环境变量：`deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | true、false | 默认设置为 `false`。 设置为 `true` 时，上传到云存储完成后将自动删除数据。 <br><br> 环境变量：`deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

此设置的名称为 `deviceAutoDeleteProperties`。 如果使用 IoT Edge 模拟器，请将这些属性的值设置为相关环境变量，可以在说明部分中找到它们。

| 属性 | 可能的值 | 说明 |
| ----- | ----- | ---- |
| deleteOn | true、false | 默认设置为 `false`。 若要启用此功能，请将此字段设置为 `true`。 <br><br> 环境变量：`deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | 以分钟为单位指定时间。 达到此值时，模块会自动删除本地存储中的 Blob。 <br><br> 环境变量：`deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true、false | 默认情况下，它设置为 `true`。在 deleteAfterMinutes 分钟过后，它会保留上传到云存储的 Blob。 可以将它设置为 `false`。在 deleteAfterMinutes 分钟过后，它会删除数据。 注意：若要使用此属性，应将 uploadOn 设置为 true。 <br><br> 环境变量：`deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>使用 SMB 共享作为本地存储
在 Windows 主机上部署此模块的 Windows 容器时，可以提供 SMB 共享作为本地存储路径。

确保 SMB 共享和 IoT 设备位于相互信任的域中。

可以运行 `New-SmbGlobalMapping` PowerShell 命令来映射运行 Windows 的 IoT 设备本地的 SMB 共享。

下面是配置步骤：
```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```
示例： <br>
`$creds = Get-Credential` <br>
`New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G: `

此命令使用凭据对远程 SMB 服务器进行身份验证。 然后，将远程共享路径映射到 G: 驱动器号（可以是任何其他可用的驱动器号）。 现在，IoT 设备的数据卷已映射到 G: 驱动器上的路径。 

确保 IoT 设备中的用户可以读取/写入远程 SMB 共享。

在部署中，`<storage mount>` 的值可以是 **G:/ContainerData:C:/BlobRoot**。 

## <a name="granting-directory-access-to-container-user-on-linux"></a>对 Linux 上的容器用户授予目录访问权限
如果在 Linux 容器的 create 选项中对存储使用了[卷装入点](https://docs.docker.com/storage/volumes/)作为存储，则无需执行任何额外的步骤；但如果使用了[绑定装入点](https://docs.docker.com/storage/bind-mounts/)，则需要执行这些步骤才能正常运行服务。

按照最低权限原则将用户的访问权限限制为用户执行其工作所需的最低权限，此模块包括用户（名称： absie、ID：11000）和用户组（name： absie，ID：11000）。 如果容器以 **root** 身份启动（默认用户为 **root**），则我们的服务将以低特权的 **absie** 用户身份启动。 

由于存在此行为，要使服务正常运行，对主机路径绑定的权限配置至关重要，否则服务将会崩溃并出现拒绝访问错误。 目录绑定中使用的路径需可由容器用户（例如 absie 11000）访问。 可以通过在主机上执行以下命令，来为容器用户授予对目录的访问权限：

```terminal
sudo chown -R 11000:11000 <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

示例：<br>
`sudo chown -R 11000:11000 /srv/containerdata` <br>
`sudo chmod -R 700 /srv/containerdata `


如果需要以非 **absie** 的用户身份运行服务，可以在部署清单中“User”属性下的 createOptions 中指定自定义用户 ID。 在这种情况下，需要使用默认组 ID 或 root 组 ID `0`。

```json
"createOptions": { 
  "User": "<custom user ID>:0" 
} 
```
现在，为容器用户授予对目录的访问权限
```terminal
sudo chown -R <user ID>:<group ID> <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

## <a name="configure-log-files"></a>配置日志文件

若要了解如何为模块配置日志文件，请参阅这些[生产最佳做法](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)。

## <a name="connect-to-your-blob-storage-module"></a>连接到 blob 存储模块

可以使用为模块配置的帐户名和帐户密钥来访问 IoT Edge 设备上的 blob 存储。

将你的 IoT Edge 设备指定为对其进行的任何存储请求的 blob 终结点。 你可以使用 IoT Edge 设备信息和配置的帐户名[为显式存储终结点创建连接字符串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。

- 对于部署在设备上的模块，如果该设备上运行 IoT Edge 上的 Azure Blob 存储模块，则 Blob 终结点为 `http://<module name>:11002/<account name>`。
- 对于在其他设备上运行的模块或应用程序，必须为网络选择正确的终结点。 根据网络设置选择终结点格式，使来自外部模块或应用程序的数据流量可以抵达 IoT Edge 模块上运行 Azure Blob 存储的设备。 此方案的 Blob 终结点是下列其中一项：
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 存储快速入门示例

Azure Blob 存储文档包括多种语言的快速入门示例代码。 可以通过将 Blob 终结点更改为连接到本地 Blob 存储模块来运行这些示例，以测试 IoT Edge 上的 Azure Blob 存储。

以下快速入门示例使用 IoT Edge 也同样支持的语言，因此，你可以将它们作为 IoT Edge 模块与 Blob 存储模块一起部署：

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
    - 使用此 SDK 时遇到已知问题，因为此版本的模块不会返回 blob 创建时间。 因此，很少方法（如列出 blob）不起作用。 解决方法是，将 blob 客户端上的 API 版本显式设置为“2017-04-17”。 <br>示例：`block_blob_service._X_MS_VERSION = '2017-04-17'`
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-v10.md)
- [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-v10.md)
- [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
- [Go](../storage/blobs/storage-quickstart-blobs-go.md)
- [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>通过 Azure 存储资源管理器连接到本地存储

可以使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)连接到本地存储帐户。

1. 下载并安装 Azure 存储资源管理器

1. 使用连接字符串连接到 Azure 存储

1. 提供连接字符串：`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 完成用于连接的步骤。

1. 在本地存储帐户中创建容器

1. 开始将文件作为块 Blob 上传。
   > [!NOTE]
   > 此模块不支持页 Blob。

1. 也可以选择在存储资源管理器中连接 Azure 存储帐户。 此配置提供适用于本地存储帐户和 Azure 存储帐户的单一视图。

## <a name="supported-storage-operations"></a>受支持的存储操作

IoT Edge 上的 Blob 存储模块使用 Azure 存储 SDK，并与适用于块 Blob 终结点的 2017-04-17 版 Azure 存储 API 保持一致。 

由于并非所有 Azure Blob 存储操作都受 IoT Edge 上的 Azure Blob 存储支持，因此此部分列出了每项操作的状态。

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

这是此模块[在 Docker 中心的发行说明](https://hub.docker.com/_/microsoft-azure-blob-storage)

## <a name="feedback"></a>反馈

你的反馈对我们很重要，我们的目标是使此模块及其功能有用且易用。 请分享你的反馈并告诉我们该如何改进。

你可以通过 absiotfeedback@microsoft.com 联系我们

## <a name="next-steps"></a>后续步骤

了解如何[在 IoT Edge 上部署 Azure Blob 存储](how-to-deploy-blob.md)

关注 [IoT Edge 上的Azure Blob 存储博客](https://aka.ms/abs-iot-blogpost)，随时了解最新的更新和公告
