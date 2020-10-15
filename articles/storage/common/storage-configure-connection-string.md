---
title: 配置连接字符串
titleSuffix: Azure Storage
description: 为 Azure 存储帐户配置连接字符串。 连接字符串包含在运行时使用共享密钥授权从应用程序访问存储帐户所需的信息。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 3f2fa6cffd63811df596c8fca80afdc888a0eea9
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92091604"
---
# <a name="configure-azure-storage-connection-strings"></a>配置 Azure 存储连接字符串

连接字符串包含应用程序在运行时使用共享密钥授权访问 Azure 存储帐户中的数据所需的授权信息。 可以将连接字符串配置为：

* 连接到 Azurite 存储模拟器。
* 在 Azure 中访问存储帐户。
* 通过共享访问签名 (SAS) 访问 Azure 中的指定资源。

若要了解如何查看帐户访问密钥并复制连接字符串，请参阅[管理存储帐户访问密钥](storage-account-keys-manage.md)。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="store-a-connection-string"></a>存储连接字符串

应用程序需要在运行时访问连接字符串，才能授权对 Azure 存储发出的请求。 可使用多个选项来存储连接字符串：

* 可以将连接字符串存储在环境变量中。
* 在桌面或设备上运行的应用程序可在 **app.config** 或 **web.config** 文件中存储连接字符串。 将连接字符串添加到这些文件中的 **AppSettings** 节。
* 在 Azure 云服务中运行的应用程序可在 [Azure 服务配置架构 (.cscfg) 文件](https://msdn.microsoft.com/library/ee758710.aspx)中存储连接字符串。 将连接字符串添加到服务配置文件的 **ConfigurationSettings** 节。

在一个配置文件中存储连接字符串可以轻松地更新连接字符串，从而在 [Azurite 存储模拟器](../common/storage-use-azurite.md)和云中的 Azure 存储帐户之间切换。 只需编辑连接字符串，使其指向目标环境。

可以使用 [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) 在运行时访问连接字符串，而不考虑应用程序在何处运行。

## <a name="configure-a-connection-string-for-azurite"></a>为 Azurite 配置连接字符串

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

有关 Azurite 的详细信息，请参阅[使用 Azurite 模拟器进行本地 Azure 存储开发](../common/storage-use-azurite.md)。

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>为 Azure 存储帐户配置连接字符串

若要为 Azure 存储帐户创建连接字符串，请使用以下格式。 指示要通过 HTTPS（建议）还是 HTTP 连接到存储帐户，将 `myAccountName` 替换为存储帐户的名称，将 `myAccountKey` 替换为帐户访问密钥：

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

例如，连接字符串可能如下所示：

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

尽管 Azure 存储支持在连接字符串中使用 HTTP 和 HTTPS，但我们*强烈建议使用 HTTPS*。

> [!TIP]
> 可以在 [Azure 门户](https://portal.azure.com)中找到存储帐户的连接字符串。 在存储帐户的菜单边栏选项卡中导航到“设置” > “访问密钥”，即可看到主访问密钥和辅助访问密钥的连接字符串。 
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>使用共享访问签名创建连接字符串

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>为显式存储终结点创建连接字符串

可以在连接字符串中指定显式服务终结点，而不使用默认终结点。 若要创建指定显式终结点的连接字符串，请使用以下格式为每个服务指定完整的服务终结点，包括协议规范（HTTPS（建议）或 HTTP）：

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

如果已将 Blob 存储终结点映射到[自定义域](../blobs/storage-custom-domain-name.md)，可能需要指定显式终结点。 在这种情况下，可以在连接字符串中指定 Blob 存储的自定义终结点。 可以选择性指定其他服务的默认终结点（如果应用程序使用这些服务）。

下面是用于指定 Blob 服务的显式终结点的连接字符串的示例：

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

此示例指定所有服务（包括 Blob 服务的自定义域）的显式终结点：

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

连接字符串中的终结点值用于构造存储服务的请求 URI，以及指示返回到代码的任何 URI 形式。

如果已将某个存储终结点映射到自定义域并在连接字符串中省略该终结点，则无法使用该连接字符串从代码访问该服务中的数据。

有关为 Azure 存储配置自定义域的详细信息，请参阅将 [自定义域映射到 Azure Blob 存储终结点](../blobs/storage-custom-domain-name.md)。

> [!IMPORTANT]
> 连接字符串中的服务终结点值必须是格式正确的 URI，包括 `https://`（推荐）或 `http://`。

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>创建带有终结点后缀的连接字符串

若要针对具有不同终结点后缀的区域或实例内的存储服务创建连接字符串，例如针对 Azure 中国世纪互联或 Azure 政府，请使用以下连接字符串格式。 指明是要通过 HTTPS（建议）还是 HTTP 连接到存储帐户，将 `myAccountName` 替换为存储帐户的名称，将 `myAccountKey` 替换为帐户访问密钥，将 `mySuffix` 替换为 URI 后缀：

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

下面是 Azure 中国世纪互联的存储服务的示例连接字符串：

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>分析连接字符串

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>后续步骤

* [使用 Azurite 模拟器进行本地 Azure 存储开发](../common/storage-use-azurite.md)
* [Azure 存储资源管理器](storage-explorers.md)
* [使用共享访问签名 (SAS)](storage-sas-overview.md)
