---
title: 渗透测试 |Microsoft Docs
description: 本文概述了渗透测试过程，以及如何对 Azure 基础结构中运行的应用执行笔测试。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2020
ms.author: terrylan
ms.openlocfilehash: dfacf124f8db0e5323c9abff56c4a78f85f6f014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816127"
---
# <a name="penetration-testing"></a>渗透测试

使用 Azure 进行应用程序测试和部署的一个优点是可快速创建环境。 不必为请求、获取以及“搭架和堆叠”本地硬件担心。

快速创建环境非常好，但仍需确保执行正常的安全截止。 你可能想要做的事情之一就是对部署在 Azure 中的应用程序进行渗透测试。

用户可能已经知道，Microsoft 将执行[对 Azure 环境的渗透测试](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)。 此测试可帮助推动 Azure 的改进。

我们不会对你的应用程序进行渗透测试，但我们知道你需要并且需要在你自己的应用程序上执行测试。 这是好事，因为改进自己的应用程序的安全性可以加强整个 Azure 生态系统的安全性。

从2017年6月15日起，Microsoft 不再需要预先批准即可对 Azure 资源进行渗透测试。 本流程仅与 Microsoft Azure 相关，并不适用于任何其他 Microsoft 云服务。

>[!IMPORTANT]
>虽然参加渗透测试时无需再通知 Microsoft，客户仍须遵守 [Microsoft 云统一渗透测试参与规则](https://technet.microsoft.com/mt784683)。

可以执行的标准测试包括：

* 对终结点进行测试，以发现[开放 Web 应用程序安全项目 (OWASP) 的前 10 个漏洞](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* 终结点的[模糊测试](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* 终结点的[端口扫描](https://en.wikipedia.org/wiki/Port_scanner)

您无法执行的一种笔测试是 [ (DoS) 攻击的拒绝服务 ](https://en.wikipedia.org/wiki/Denial-of-service_attack) 。 此测试包括发起 DoS 攻击，或者执行相关的测试，以便确定、演示或模拟任何类型的 DoS 攻击。

>[!Note]
>Mircosoft 已与 BreakingPoint Cloud 合作构建接口，用户可在其中针对已启用 DDoS 保护的公共 IP 地址生成用于模拟的流量。 若要了解有关断点云模拟的详细信息，请参阅 [验证 DDoS 检测](../../virtual-network/manage-ddos-protection.md#validate-ddos-detection)。

## <a name="next-steps"></a>后续步骤

* 详细了解 Engagement 的 [渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2)。
