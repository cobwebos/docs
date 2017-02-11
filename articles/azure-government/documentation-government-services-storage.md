---
title: "Azure 政府版存储 | Microsoft Docs"
description: "这里提供了针对 Azure Government 的应用程序开发使用到的功能和指南对比"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/13/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 004860b319b7b32955415ac2d6c7308c32037848
ms.openlocfilehash: 36312ce195e931d174e00d54cab4cb071a85b618


---
# <a name="azure-government-storage"></a>Azure 政府版存储
## <a name="azure-storage"></a>Azure 存储空间
有关此服务以及如何使用此服务的详细信息，请参阅 [Azure 存储公共文档](../storage/index.md)。

### <a name="variations"></a>变体
Azure Government 中的存储帐户 URL 不相同：

| 服务类型 | Azure Public | Azure Government  |
| --- | --- | --- |
| Blob 存储 |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| 队列存储 |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| 表存储 |*.table.core.windows.net |*.table.core.usgovcloudapi.net |

> [!NOTE]
> 所有脚本和代码均需要说明相应终结点。  请参阅[配置 Azure 存储连接字符串](../storage/storage-configure-connection-string.md)。 
>
>

有关 API 的详细信息，请参阅<a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">云存储帐户构造函数</a>。

在这些重载中使用的终结点后缀是 core.usgovcloudapi.net

### <a name="considerations"></a>注意事项
以下信息标识 Azure 存储的 Azure Government 边界：

| 允许的管制/控制数据 | 不允许的管制/控制数据 |
| --- | --- |
| 在 Azure 存储产品内输入、存储及处理的数据可包含导出控制数据。 静态身份验证器，例如用于访问 Azure 平台组件的密码和智能卡 PIN。 用于管理 Azure 平台组件的证书私钥。 其他安全信息/机密信息，例如证书、加密密钥、主密钥和 Azure 服务中存储的存储密钥。 |Azure 存储元数据不允许包含导出控制数据。 此元数据包括创建和维护存储产品时输入的所有配置数据。  不要将管理/控制数据输入到以下字段：资源组、部署名称、资源名称、资源标记 |

## <a name="premium-storage"></a>高级存储
有关此服务及如何使用此服务的详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../storage/storage-premium-storage.md)。

### <a name="variations"></a>变体
高级存储已在美国弗吉尼亚州正式发布。 这包括 DS 系列虚拟机。

### <a name="considerations"></a>注意事项
上面列出的存储数据的注意事项同样适用于高级存储帐户。

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 博客</a>。



<!--HONumber=Nov16_HO3-->


