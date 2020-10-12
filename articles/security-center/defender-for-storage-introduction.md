---
title: 用于存储的 Azure Defender - 优点和功能
description: 了解用于存储的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 4677426337a48d4fde74f61b8a4ad6fcb695f420
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577809"
---
# <a name="introduction-to-azure-defender-for-storage"></a>用于存储的 Azure Defender 简介

**用于存储的 Azure Defender** 会检测 Azure 存储帐户上可能存在的有害活动。 无论数据是存储为 blob 容器、文件共享还是数据湖，都可以为其提供保护。

无需成为安全专家，就能利用此保护层来应对威胁，它还能帮助你管理安全监视系统。


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|**用于存储的 Azure Defender** 按[定价页中](security-center-pricing.md)的定价计费|
|受保护的存储类型：|[Blob 存储](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure 文件](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) China Gov，其他 Gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>用于存储的 Azure Defender 提供哪种类型的警报？

当存在以下情况时，会触发安全警报：

- **可疑活动** - 例如，有人从已知为 Tor 主动退出节点（匿名化代理）的 IP 地址成功访问存储帐户
- **异常行为** - 例如，对某个存储帐户的访问模式发生更改
- **上传了可能是恶意软件的软件** - 哈希信誉分析结果指示某个上传的文件中包含恶意软件

警报包含触发警报的事件的详细信息，并提供有关如何调查和消除威胁的建议。

> [!TIP]
> 可以按照[此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)中的说明来模拟存储警报。


## <a name="what-is-hash-reputation-analysis-for-malware"></a>什么是针对恶意软件的哈希信誉分析？

为确定上传的文件是否可疑，用于存储的 Azure Defender 使用由 [Microsoft 威胁情报](https://go.microsoft.com/fwlink/?linkid=2128684)提供支持的哈希信誉分析。 威胁防护工具不扫描上传的文件，而是检查存储日志，并将新上传的文件的哈希值与已知病毒、木马、间谍软件和勒索软件的哈希值进行比较。 

当怀疑某个文件包含恶意软件时，安全中心会显示一个警报，并且可以选择向存储所有者发送电子邮件，请求其批准删除该可疑文件。 若要设置为自动删除哈希信誉分析指示为包含恶意软件的文件，请部署[工作流自动化，以便在出现包含“可能有恶意软件上传到存储帐户”信息的警报时触发操作](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)。

> [!NOTE]
> 若要启用安全中心的威胁保护功能，必须在包含适用工作负载的订阅上启用 Azure Defender。
>
> 可以在订阅级别或资源级别启用**用于存储的 Azure Defender**。



## <a name="next-steps"></a>后续步骤

本文介绍了用于存储的 Azure Defender。

如需相关材料，请参阅以下文章： 

- 无论警报是由安全中心生成，还是由安全中心从其他安全产品接收，你都可以导出该警报。 若要将警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。
- [如何启用用于存储的 Advanced Defender](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [用于存储的 Azure Defender 警报列表](alerts-reference.md#alerts-azurestorage)
- [Microsoft 的威胁情报功能](https://go.microsoft.com/fwlink/?linkid=2128684)