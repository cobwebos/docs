---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75469448"
---
### <a name="azure-storage-linked-service"></a>Azure 存储链接服务
**Azure 存储链接服务**可让你使用**帐户密钥**（为数据工厂提供 Azure 存储的全局访问权限）将 Azure 存储帐户链接到 Azure 数据工厂。 下表提供 Azure 存储链接服务专属 JSON 元素的描述。

| properties | 说明 | 必需 |
|:--- |:--- |:--- |
| type |Type 属性必须设置为： **AzureStorage** |是 |
| connectionString |为 connectionString 属性指定连接到 Azure 存储所需的信息。 |是 |

有关如何检索存储帐户访问密钥的信息，请参阅[管理存储帐户访问密钥](../articles/storage/common/storage-account-keys-manage.md)。

**示例：**  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azure-storage-sas-linked-service"></a>Azure 存储 SAS 链接服务
共享访问签名 (SAS) 用于对存储帐户中的资源进行委托访问。 这样便可以授权客户端在指定时间段内，以一组指定权限有限地访问存储帐户中的对象，而不必共享帐户访问密钥。 SAS 是一个 URI，在其查询参数中包含对存储资源已验证访问所需的所有信息。 要使用 SAS 访问存储资源，客户端只需将 SAS 传入到相应的构造函数或方法。 有关 SAS 的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../articles/storage/common/storage-sas-overview.md)。

> [!IMPORTANT]
> Azure 数据工厂现仅支持**服务 SAS**，而不支持帐户 SAS。 请注意，通过 Azure 门户或存储资源管理器生成的 SAS URL 是不受支持的帐户 SAS。

> [!TIP]
> 可执行以下 PowerShell 命令为存储帐户生成服务 SAS（替换占位符并授予所需权限）：`$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Azure 存储 SAS 链接服务可让你使用共享访问签名 (SAS) 将 Azure 存储帐户链接到 Azure 数据工厂。 这样，便可以将存储中所有/特定资源（Blob/容器）的受限/限时访问权限提供给数据工厂。 下表提供了 Azure 存储 SAS 链接服务特定的 JSON 元素的描述。 

| properties | 说明 | 必需 |
|:--- |:--- |:--- |
| type |type 属性必须设置为：**AzureStorageSas** |是 |
| sasUri |指定 Azure 存储资源（例如 Blob、容器或表）的共享访问签名 URI。  |是 |

**示例：**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

创建 **SAS URI** 时，请注意以下事项：  

* 根据链接服务（读取、写入、读/写）在数据工厂中的使用方式，设置对对象的适当读/写**权限**。
* 根据需要设置“到期时间”****。 确保 Azure 存储对象的访问权限不会在管道的活动期限内过期。
* 应该根据需要在正确的容器/Blob 或表级别创建 URI。 数据工厂服务可以使用 Azure Blob 的 SAS URI 访问特定的 Blob。 数据工厂服务可以使用 Azure Blob 容器的 SAS URI 迭代该容器中的 Blob。 如果稍后需要提供更多/更少对象的访问权限或需要更新 SAS URI，请记得使用新 URI 更新链接服务。   

