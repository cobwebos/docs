---
title: 用于存储的 Azure Defender-优点和功能
description: 了解 Azure Defender 用于存储的优势和功能。
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 31d1bde1555f98164ccba32d4615ba51837c5ef7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934807"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Azure Defender for Storage 简介

**用于存储的 Azure Defender** 检测到你的 azure 存储帐户有潜在有害的活动。 无论数据存储为 blob 容器、文件共享还是数据 lake，都可以对其进行保护。

这一层保护使你 *无* 需成为安全专家，就能解决威胁，并可帮助你管理安全监视系统。


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|计价|**Azure Defender For Storage**按[定价页](security-center-pricing.md)中所示方式计费|
|受保护的存储类型|[Blob 存储](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure 文件](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中国 Gov，其他 Gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Azure Defender for Storage 提供了何种类型的警报？

当存在以下情况时，会触发安全警报：

- **可疑活动** -例如，存储帐户已从称为 Tor 的活动退出节点的 IP 地址成功访问
- **异常行为** -例如，对存储帐户的访问模式的更改
- 已**上传潜在恶意软件**-哈希信誉分析表明上传的文件包含恶意软件

警报包含触发这些事件的事件的详细信息，并提供有关如何调查和修正威胁的建议。

> [!TIP]
> 可以按照 [此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)中的说明来模拟存储警报。


## <a name="what-is-hash-reputation-analysis-for-malware"></a>什么是恶意软件的哈希信誉分析？

若要确定上传的文件是否可疑，Azure Defender for Storage 使用 [Microsoft 威胁情报](https://go.microsoft.com/fwlink/?linkid=2128684)支持的哈希信誉分析。 威胁防护工具不会扫描已上传的文件，而是检查存储日志，并将新上载文件的哈希与已知病毒、特洛伊木马程序、间谍软件和勒索软件的哈希进行比较。 

当怀疑某个文件包含恶意软件时，安全中心将显示一个警报，还可以选择通过电子邮件发送存储所有者以批准删除可疑文件。 若要设置自动删除哈希信誉分析指出包含恶意软件的文件，请部署 [工作流自动化，使其在包含 "可能已上传到存储帐户的恶意软件" 的警报上触发](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)。

> [!NOTE]
> 若要启用安全中心的威胁防护功能，必须在包含适用工作负荷的订阅上启用 Azure Defender。
>
> 可以在订阅级别或资源级别启用 **用于存储的 Azure Defender** 。



## <a name="next-steps"></a>后续步骤

本文介绍了用于存储的 Azure Defender。

如需相关材料，请参阅以下文章： 

- 无论警报是由安全中心生成，还是由安全中心从其他安全产品接收，你都可以导出该警报。 若要将警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。
- [如何启用用于存储的高级 Defender](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [Azure Defender for Storage 警报列表](alerts-reference.md#alerts-azurestorage)
- [Microsoft 的威胁情报功能](https://go.microsoft.com/fwlink/?linkid=2128684)