---
title: "Azure 政府版存储 | Microsoft Docs"
description: "这里提供了针对 Azure Government 的应用程序开发使用到的功能和指南对比"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/22/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 831334f2b835d00aa3fd22292764b69e85d735a6
ms.openlocfilehash: a6b61df5884031eb5b53f983b834357bd23a622e
ms.lasthandoff: 02/08/2017


---
# <a name="azure-government-storage"></a>Azure 政府版存储
## <a name="azure-storage"></a>Azure 存储空间
有关此服务以及如何使用此服务的详细信息，请参阅 [Azure 存储公共文档](../storage/index.md)。

### <a name="storage-service-availability-by-azure-government-region"></a>Azure Government Region 可实现的存储服务可用性

| 服务 | USGov Virginia | USGov Iowa | 说明
| --- | --- | --- | --- |
| [Blob 存储] (../storage/storage-introduction.md#blob-storage) |GA |GA |
| [表存储] (../storage/storage-introduction.md#table-storage) |GA  |GA |
| [队列存储] (../storage/storage-introduction.md#queue-storage) |GA | GA |
| [文件存储] (../storage/storage-introduction.md#file-storage) |GA |GA |
| [热/冷 Blob 存储] (../storage/storage-blob-storage-tiers.md) |不可用 |不可用 |
| [存储服务加密] (../storage/storage-service-encryption.md) |GA |GA |
| [高级存储] (../storage/storage-premium-storage.md) |GA |不可用 | 包括 DS 系列虚拟机。 |
| [Blob 导入/导出] (../storage/storage-import-export-service.md) |GA |GA |
| [StorSimple] (../storsimple/storsimple-ova-overview.md) |GA |GA |

### <a name="variations"></a>变体
Azure Government 中的存储帐户 URL 不相同：

| 服务类型 | Azure Public | Azure Government  |
| --- | --- | --- |
| Blob 存储 |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| 队列存储 |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| 表存储 |*.table.core.windows.net |*.table.core.usgovcloudapi.net |
| 文件存储 |*.file.core.windows.net |*.file.core.usgovcloudapi.net | 

> [!NOTE]
> 所有脚本和代码均需要说明相应终结点。  请参阅[配置 Azure 存储连接字符串](../storage/storage-configure-connection-string.md)。 
>
>

有关 API 的详细信息，请参阅<a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">云存储帐户构造函数</a>。

在这些重载中使用的终结点后缀是 core.usgovcloudapi.net

> [!NOTE]
> [Microsoft Azure 存储资源管理器] (../vs-azure-tools-storage-manage-with-storage-explorer.md) 当前不支持通过在 Azure Government 中添加帐户来 [连接到 Azure 订阅] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-an-azure-subscription)。 使用其他用于 [连接到存储帐户] 的方法 (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)。
[附加到外部存储帐户] (../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-to-an-external-storage-account) 时，在“第 3 步”处选择“其他(在下方指定)”作为存储终结点域，并为 Azure Government 指定“core.usgovcloudapi.net”。
>
>

> [!NOTE]
> 如果在 [装载文件共享] (.../ storage/storage-dotnet-how-to-use-files.md#mount-the-file-share) 时 返回错误&53;“未找到网络路径”。 可能是由于防火墙阻止了出站端口。 请尝试在位于同一 Azure 订阅中的 VM 上装载该文件共享作为存储帐户。
>
>

> [!NOTE]
> 部署 StorSimple Manager 服务时，请对 Azure 门户和经典门户分别使用 https://portal.azure.us/ 和 https://manage.windowsazure.us/ URL。 有关 StorSimple Virtual Array 的部署说明，请参阅 [StorSimple Virtual Array 系统要求](../storsimple/storsimple-ova-system-requirements.md)；有关 StorSimple 8000 系列的部署说明，请参阅 [StorSimple 软件、高可用性和网络要求](../storsimple/storsimple-system-requirements.md)，并从左侧导航转到“部署”部分。 有关常规的 StorSimple 文档，请参阅[什么是 StorSimple？](../storsimple/index.md)。
>
>

### <a name="considerations"></a>注意事项
以下信息标识 Azure 存储的 Azure Government 边界：

| 允许的管制/控制数据 | 不允许的管制/控制数据 |
| --- | --- |
| 在 Azure 存储产品内输入、存储及处理的数据可包含导出控制数据。 静态身份验证器，例如用于访问 Azure 平台组件的密码和智能卡 PIN。 用于管理 Azure 平台组件的证书私钥。 其他安全信息/机密信息，例如证书、加密密钥、主密钥和 Azure 服务中存储的存储密钥。 |Azure 存储元数据不允许包含导出控制数据。 此元数据包括创建和维护存储产品时输入的所有配置数据。  不要将管理/控制数据输入到以下字段：资源组、部署名称、资源名称、资源标记 |

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 博客</a>。

