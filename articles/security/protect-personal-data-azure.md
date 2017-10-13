---
title: "在 Microsoft Azure 中保护个人数据 | Microsoft Docs"
description: "介绍如何使用 Azure 保护个人数据的系列教程中的第一篇文章"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 4dbdb2dc11bdc515fb3856dd45203868122c7726
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-in-microsoft-azure"></a>在 Microsoft Azure 中保护个人数据

本文介绍一系列可帮助读者使用 Azure 安全技术和服务来保护个人数据的文章。 许多企业和行业法规遵从与监管措施中都规定了这一项关键要求。 本文描述了方案、问题陈述和公司目标。

## <a name="scenario-and-problem-statement"></a>方案与问题陈述

总部位于美国的一家大型邮轮公司正在拓展其运营，以便在地中海、亚得里亚海和波罗的海以及英属岛屿提供路线。 为支持这些工作，该公司并购了意大利、德国、丹麦和英国的几家小型邮轮公司。

该公司使用 Microsoft Azure 在云中存储企业数据。 这可能包括如下所述的客户和/或员工信息：

- 地址
- 电话号码
- 税务识别号
- 信用卡信息

该公司必须保护客户和员工数据的隐私，同时，可让相应的部门在有需要时访问数据。 （例如薪酬和预订部门）

## <a name="company-goals"></a>公司目标 

- 包含个人数据的数据源在放入云存储中时经过加密。

- 将个人数据从一个位置转移到另一个位置时进行传输中加密。 如果在企业数据中心与 Azure 云之间通过虚拟网络或 Internet 传输数据，则需要采取此措施。

- 通过强大的标识管理和访问控制技术保护个人数据的机密性和完整性，防止未经授权的访问。

- 通过监视漏洞和威胁来防止有人通过数据破解透露个人数据。

- 评估用于存储或传输个人数据的 Azure 服务的安全状态，以找到可以更好保护个人数据的机会。

## <a name="data-protection-guidance"></a>数据保护指南

以下文章包含可帮助实现上述个人数据保护目标的技术性操作指南：

- [Azure 加密技术](protect-personal-data-at-rest.md)

- [Azure 加密技术](protect-personal-data-in-transit-encryption.md)

- [Azure 标识和访问技术](protect-personal-data-identity-access-controls.md)

- [Azure 网络安全技术](protect-personal-data-network-security.md)

- [Azure 安全中心](protect-personal-data-azure-security-center.md)



## <a name="next-steps"></a>后续步骤

- [Azure 安全信息站点](https://aka.ms/AzureSecInfo)

- [Microsoft 信任中心](https://www.microsoft.com/TrustCenter/default.aspx)

- [Azure 安全中心](https://azure.microsoft.com/services/security-center/)

- [Azure 安全团队博客](https://www.azuresecurityorg)

- [Azure.com 博客 - 安全](https://azure.microsoft.com/blog/topics/security/)
