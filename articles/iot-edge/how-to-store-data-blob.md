---
title: 将块 Blob 存储在设备上 - Azure IoT Edge |Microsoft Docs
description: Understand tiering and time-to-live features, see supported blob storage operations, and connect to your blob storage account.
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
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Store data at the edge with Azure Blob Storage on IoT Edge

IoT Edge 上的 Azure Blob 存储在边缘提供了[块 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 存储解决方案。 A blob storage module on your IoT Edge device behaves like an Azure block blob service, except the block blobs are stored locally on your IoT Edge device. 你可以使用相同的 Azure 存储 SDK 方法或已经习惯的块 blob API 调用来访问 blob。 This article explains the concepts related to Azure Blob Storage on IoT Edge container that runs a blob service on your IoT Edge device.

This module is useful in scenarios:
* where data needs to be stored locally until it can be processed or transferred to the cloud. This data can be videos, images, finance data, hospital data, or any other unstructured data.
* when devices are located in a place with limited connectivity.
* when you want to efficiently process the data locally to get low latency access to the data, such that you can respond to emergencies as quickly as possible.
* when you want to reduce bandwidth costs and avoid transferring terabytes of data to the cloud. You can process the data locally and send only the processed data to the cloud.

Watch the video for quick introduction
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

This module comes with **deviceToCloudUpload** and **deviceAutoDelete** features.

**deviceToCloudUpload** is a configurable functionality. This function automatically uploads the data from your local blob storage to Azure with intermittent internet connectivity support. It allows you to:

- Turn ON/OFF the deviceToCloudUpload feature.
- Choose the order in which the data is copied to Azure like NewestFirst or OldestFirst.
- Specify the Azure Storage account to which you want your data uploaded.
- Specify the containers you want to upload to Azure. This module allows you to specify both source and target container names.
- Choose the ability to delete the blobs immediately, after upload to cloud storage is finished
- Do full blob upload (using `Put Blob` operation) and block level upload (using `Put Block` and `Put Block List` operations).

This module uses block level upload, when your blob consists of blocks. Here are some of the common scenarios:

- Your application updates some blocks of a previously uploaded blob, this module uploads only the updated blocks and not the whole blob.
- The module is uploading blob and internet connection goes away, when the connectivity is back again it uploads only the remaining blocks and not the whole blob.

If an unexpected process termination (like power failure) happens during a blob upload, all blocks that were due for the upload will be uploaded again once the module comes back online.

**deviceAutoDelete** is a configurable functionality. This function automatically deletes your blobs from the local storage when the specified duration (measured in minutes) expires. It allows you to:

- Turn ON/OFF the deviceAutoDelete feature.
- Specify the time in minutes (deleteAfterMinutes) after which the blobs will be automatically deleted.
- Choose the ability to retain the blob while it's uploading if the deleteAfterMinutes value expires.


## <a name="prerequisites"></a>必备组件

Azure IoT Edge 设备：

- You can use your development machine or a virtual machine as an IoT Edge device by following the steps in the quickstart for [Linux](quickstart-linux.md) or [Windows devices](quickstart.md).

- Refer to [Azure IoT Edge supported systems](support.md#operating-systems) for a list of supported operating systems and architectures. The Azure Blob Storage on IoT Edge module supports following architectures:
    - Windows AMD64
    - Linux AMD64
    - Linux ARM32
    - Linux ARM64 (preview)

云资源：

Azure 中的标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload and deviceAutoDelete properties

Use the module's desired properties to set **deviceToCloudUploadProperties** and **deviceAutoDeleteProperties**. Desired properties can be set during deployment or changed later by editing the module twin without the need to redeploy. We recommend checking the "Module Twin" for `reported configuration` and `configurationValidation` to make sure values are correctly propagated.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

The name of this setting is `deviceToCloudUploadProperties`. If you are using the IoT Edge simulator, set the values to the related environment variables for these properties, which you can find in the explanation section.

| properties | 可能的值 | 说明 |
| ----- | ----- | ---- |
| uploadOn | true、false | Set to `false` by default. If you want to turn the feature on, set this field to `true`. <br><br> Environment variable: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Allows you to choose the order in which the data is copied to Azure. Set to `OldestFirst` by default. The order is determined by last modified time of Blob. <br><br> Environment variable: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` is a connection string that allows you to specify the storage account to which you want your data uploaded. Specify `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Add appropriate EndpointSuffix of Azure where data will be uploaded, it varies for Global Azure, Government Azure, and Microsoft Azure Stack. <br><br> You can choose to specify Azure Storage SAS connection string here. But you have to update this property when it expires. <br><br> Environment variable: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Allows you to specify the container names you want to upload to Azure. This module allows you to specify both source and target container names. If you don't specify the target container name, it will automatically assign the container name as `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. You can create template strings for target container name, check out the possible values column. <br>* %h -> IoT Hub Name (3-50 characters). <br>* %d -> IoT Edge Device ID (1 to 129 characters). <br>* %m -> Module Name (1 to 64 characters). <br>* %c -> Source Container Name (3 to 63 characters). <br><br>Maximum size of the container name is 63 characters, while automatically assigning the target container name if the size of container exceeds 63 characters it will trim each section (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) to 15 characters. <br><br> Environment variable: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | true、false | Set to `false` by default. When it is set to `true`, it will automatically delete the data when upload to cloud storage is finished. <br><br> Environment variable: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

The name of this setting is `deviceAutoDeleteProperties`. If you are using the IoT Edge simulator, set the values to the related environment variables for these properties, which you can find in the explanation section.

| properties | 可能的值 | 说明 |
| ----- | ----- | ---- |
| deleteOn | true、false | Set to `false` by default. If you want to turn the feature on, set this field to `true`. <br><br> Environment variable: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Specify the time in minutes. The module will automatically delete your blobs from local storage when this value expires. <br><br> Environment variable: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true、false | By default it is set to `true`, and it will retain the blob while it is uploading to cloud storage if deleteAfterMinutes expires. You can set it to `false` and it will delete the data as soon as deleteAfterMinutes expires. Note: For this property to work uploadOn should be set to true. <br><br> Environment variable: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Using SMB share as your local storage
You can provide SMB share as your local storage path, when you deploy Windows container of this module on Windows host.

Make sure the SMB share and IoT device are in mutually trusted domains.

You can run `New-SmbGlobalMapping` PowerShell command to map the SMB share locally on the IoT device running Windows.

Below are the configuration steps:
```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```
示例： <br>
`$creds = Get-Credential` <br>
`New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G: `

This command will use the credentials to authenticate with the remote SMB server. Then, map the remote share path to G: drive letter (can be any other available drive letter). The IoT device now have the data volume mapped to a path on the G: drive. 

Make sure the user in IoT device can read/write to the remote SMB share.

For your deployment the value of `<storage mount>` can be **G:/ContainerData:C:/BlobRoot**. 

## <a name="granting-directory-access-to-container-user-on-linux"></a>Granting directory access to container user on Linux
If you have used [volume mount](https://docs.docker.com/storage/volumes/) for storage in your create options for Linux containers then you don't have to do any extra steps, but if you used [bind mount](https://docs.docker.com/storage/bind-mounts/) then these steps are required to run the service correctly.

Following the principle of least privilege to limit the access rights for users to bare minimum permissions they need to perform their work, this module includes a user (name: absie, ID: 11000) and a user group (name: absie, ID: 11000). If the container is started as **root** (default user is **root**), our service will be started as the low-privilege **absie** user. 

This behavior makes configuration of the permissions on host path binds crucial for the service to work correctly, otherwise the service will crash with access denied errors. The path that is used in directory binding needs to be accessible by the container user (example: absie 11000). You can grant the container user access to the directory by executing the commands below on the host:

```terminal
sudo chown -R 11000:11000 <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

示例：<br>
`sudo chown -R 11000:11000 /srv/containerdata` <br>
`sudo chmod -R 700 /srv/containerdata `


If you need to run the service as a user other than **absie**, you can specify your custom user ID in createOptions under "User" property in your deployment manifest. In such case you need to use default or root group ID `0`.

```json
"createOptions": { 
  "User": "<custom user ID>:0" 
} 
```
Now, grant the container user access to the directory
```terminal
sudo chown -R <user ID>:<group ID> <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

## <a name="configure-log-files"></a>Configure log files

For information on configuring log files for your module, see these [production best practices](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>连接到 blob 存储模块

可以使用为模块配置的帐户名和帐户密钥来访问 IoT Edge 设备上的 blob 存储。

将你的 IoT Edge 设备指定为对其进行的任何存储请求的 blob 终结点。 你可以使用 IoT Edge 设备信息和配置的帐户名[为显式存储终结点创建连接字符串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。

- For modules that are deployed on the same device as where the Azure Blob Storage on IoT Edge module is running, the blob endpoint is: `http://<module name>:11002/<account name>`.
- For modules or applications running on a different device, you have to choose the right endpoint for your network. Depending on your network setup, choose an endpoint format such that the data traffic from your external module or application can reach the device running the Azure Blob Storage on IoT Edge module. The blob endpoint for this scenario is one of:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob Storage quickstart samples

The Azure Blob Storage documentation includes quickstart sample code in several languages. You can run these samples to test Azure Blob Storage on IoT Edge by changing the blob endpoint to connect to your local blob storage module.

The following quickstart samples use languages that are also supported by IoT Edge, so you could deploy them as IoT Edge modules alongside the blob storage module:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
    - We have a known issue while using this SDK because this version of the module does not return blob creation time. Hence few methods like list blobs does not work. As a workaround set explicitly API version on the blob client to '2017-04-17'. <br>示例：`block_blob_service._X_MS_VERSION = '2017-04-17'`
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-v10.md)
- [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-v10.md)
- [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
- [Go](../storage/blobs/storage-quickstart-blobs-go.md)
- [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Connect to your local storage with Azure Storage Explorer

You can use [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) to connect to your local storage account.

1. Download and install Azure Storage Explorer

1. Connect to Azure Storage using a connection string

1. Provide connection string: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Go through the steps to connect.

1. Create container inside your local storage account

1. Start uploading files as Block blobs.
   > [!NOTE]
   > This module does not support Page blobs.

1. You can choose to connect your Azure storage accounts in Storage Explorer, too. This configuration gives you a single view for both your local storage account and Azure storage account

## <a name="supported-storage-operations"></a>受支持的存储操作

Blob storage modules on IoT Edge use the Azure Storage SDKs, and are consistent with the 2017-04-17 version of the Azure Storage API for block blob endpoints. 

Because not all Azure Blob Storage operations are supported by Azure Blob Storage on IoT Edge, this section lists the status of each.

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

## <a name="event-grid-on-iot-edge-integration"></a>Event Grid on IoT Edge Integration
> [!CAUTION]
> The integration with Event Grid on IoT Edge is in preview

This Azure Blob Storage on IoT Edge module now provides integration with Event Grid on IoT Edge. For detailed information on this integration, see the [tutorial to deploy the modules, publish events and verify event delivery](../event-grid/edge/react-blob-storage-events-locally.md).

## <a name="release-notes"></a>发行说明

Here are the [release notes in docker hub](https://hub.docker.com/_/microsoft-azure-blob-storage) for this module

## <a name="feedback"></a>反馈

Your feedback is important to us to make this module and its features useful and easy to use. Please share your feedback and let us know how we can improve.

You can reach us at absiotfeedback@microsoft.com

## <a name="next-steps"></a>后续步骤

Learn how to [Deploy Azure Blob Storage on IoT Edge](how-to-deploy-blob.md)

Stay up-to-date with recent updates and announcement in the [Azure Blob Storage on IoT Edge blog](https://aka.ms/abs-iot-blogpost)
