---
title: "保护 Azure 安全中心的 Azure SQL 服务和数据 | Microsoft Docs"
description: "本文档介绍 Azure 安全中心的建议，以帮助你保护数据和 Azure SQL 服务并遵循安全策略。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 7b5df25f46a2824acb361401ab90c960b8e5978f
ms.openlocfilehash: 8ce47dd649d1d945df506ed65c871cf3afe2f004


---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>在 Azure 安全中心保护 Azure SQL 服务和数据
Azure 安全中心可分析 Azure 资源的安全状态。 在安全中心识别潜在的安全漏洞时，它会创建一些建议，这些建议会指导你完成配置所需控件的过程。  适用于 Azure 资源类型的建议：虚拟机 (VM)、网络、SQL 和数据，以及应用程序。

本文介绍适用于 Azure SQL 服务和数据的建议。 启用 Azure SQL 服务器和数据库审核、启用 SQL 数据库加密和启用 Azure 存储帐户加密的建议中心。  使用下表作为参考，以便了解可用的 SQL 服务和数据的建议，以及应用建议后每个建议的做法。

## <a name="available-sql-service-and-data-recommendations"></a>可用的 SQL 服务和数据的建议
| 建议 | 说明 |
| --- | --- |
| [启用服务器 SQL 审核](security-center-enable-auditing-on-sql-servers.md) |建议启用 Azure SQL 服务器审核（仅限 Azure SQL 服务；不包括虚拟机上运行的 SQL）。 |
| [启用数据库 SQL 审核](security-center-enable-auditing-on-sql-databases.md) |建议启用 Azure SQL 数据库审核（仅限 Azure SQL 服务；不包括虚拟机上运行的 SQL）。 |
| [启用 SQL 数据库的透明数据加密](security-center-enable-transparent-data-encryption.md) |建议启用 SQL 数据库加密（仅限 Azure SQL 服务）。 |
| [为 Azure 存储帐户启用加密](security-center-enable-encryption-for-storage-account.md) | 建议为静态数据启用 Azure 存储服务加密。 存储服务加密 (SSE) 的工作原理是，在将数据写入 Azure 存储时对数据进行加密，以及在检索前对数据进行解密。 SSE 当前仅适用于 Azure Blob 服务，并可用于块 blob、页 blob 和追加 blob。 若要了解详细信息，请参阅[静态数据的存储服务加密](../storage/storage-service-encryption.md)。</br>只有 Resource Manager 存储帐户支持 SSE。 目前不支持经典存储帐户。 若要了解经典部署模型和 Resource Manager 部署模型，请参阅 [Azure 部署模型](../azure-classic-rm.md)。 |

## <a name="see-also"></a>另请参阅
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：

* [保护 Azure 安全中心中的虚拟机](security-center-virtual-machine-recommendations.md)
* [保护 Azure 安全中心中的应用程序](security-center-application-recommendations.md)
* [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [Azure 安全中心常见问题](security-center-faq.md) - 查找有关使用服务的常见问题。



<!--HONumber=Jan17_HO1-->


