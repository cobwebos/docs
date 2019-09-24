---
title: Azure 安全中心的容器建议 | Microsoft Docs
description: 本文档介绍了 Azure 安全中心有关如何帮助保护容器的建议。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: memildin
ms.openlocfilehash: 96efe5271b91f87e30ba26aabe69a0de76e1a791
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202390"
---
# <a name="understand-azure-security-center-container-recommendations"></a>了解 Azure 安全中心容器建议

迁移整体应用程序以运行生产环境中的任务关键型容器化云原生应用程序时，可以利用容器的功能，包括简单快速的部署和更新。 随着部署的容器数不断增加，需要部署安全解决方案，以洞察容器的安全状态并帮助防范它们受到威胁。

Azure 安全中心提供以下功能来帮助保护容器：

- **洞察 IaaS Linux 计算机上托管的容器**<br>Azure 安全中心的“容器”选项卡会显示使用 Docker 部署的所有虚拟机。 浏览虚拟机上的安全问题时，安全中心会提供计算机上的容器相关的其他信息，例如 Docker 版本以及主机上运行的映像数。

    ![容器选项卡](./media/security-center-container-recommendations/docker-recommendation.png)


- **基于 Docker CIS 基准的安全建议**<br>安全中心扫描 Docker 配置，并提供已评估的所有失败规则列表让你洞察错误配置。 安全中心提供指导来帮助你快速解决这些问题，因此可节省时间。 安全中心持续评估 Docker 配置，并提供其最新状态。

    ![容器选项卡](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **实时容器威胁检测**<br> 安全中心为配备 AuditD 组件的 Linux 计算机上的容器提供实时威胁检测结果。 警报中会指明多种可疑的 Docker 活动，例如，在主机上创建特权容器、指示安全外壳 (SSH) 服务器在 Docker 容器中运行，或使用加密货币采矿程序。 可以使用此信息快速补救安全问题，并提高容器的安全性。

    ![容器选项卡](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>建议
使用下表作为参考，帮助了解 IaaS Linux 计算机上托管的可用容器以及 Docker 配置的安全性评估。

| 建议 | 描述 | 更正 |
| --- | --- | --- |
|修复容器安全配置中的漏洞 |根据配置的最佳做法修复容器安全配置中的漏洞。| 修复容器安全配置中的漏洞：<br>1.查看失败规则列表。<br>2.根据指定的说明修复每个规则。|


## <a name="next-steps"></a>后续步骤
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：

* [在 Azure 安全中心监视标识和访问](security-center-identity-access.md)
* [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)
* [保护 Azure 安全中心中的 Azure SQL 服务](security-center-sql-service-recommendations.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心保护计算机和应用程序](security-center-virtual-machine-protection.md)
* [在 Azure 安全中心中设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。

