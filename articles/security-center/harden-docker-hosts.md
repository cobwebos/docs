---
title: 使用 Azure 安全中心强化 Docker 主机并保护容器
description: 如何保护 Docker 主机，并验证它们是否符合 CIS Docker 基准
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 500fa45db7e0e6bffb587d9d352ee1ab49f14703
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712266"
---
# <a name="harden-your-docker-hosts"></a>强化 Docker 主机

Azure 安全中心会识别在 IaaS Linux VM 上或其他运行 Docker 容器的 Linux 计算机上承载的非托管容器。 安全中心会持续评估这些容器的配置。 然后，它会将其与 [Internet 安全中心 (CIS) 的 Docker 基准](https://www.cisecurity.org/benchmark/docker/)进行比较。

安全中心包含 CIS 的 Docker 基准的完整规则集，并会在容器不符合控件标准的情况下发出警报。 在发现错误配置时，安全中心会生成安全建议。 使用安全中心的建议页面来查看建议和修正问题。

发现漏洞后，它们会组合在单个建议内。

>[!NOTE]
> 这些 CIS 基准检查不会在 AKS 托管实例或 Databricks 托管 VM 上运行。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|需要[用于服务器的 Azure Defender](defender-for-servers-introduction.md)|
|所需角色和权限：|主机连接到的工作区中的**读取器**|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>识别和修正 Docker 配置中的安全漏洞

1. 在安全中心的菜单中，打开 " **建议** " 页。

1. 筛选到 **容器安全配置中** 建议的漏洞，然后选择建议。

    建议页面显示) 的 Docker 主机 (受影响的资源。 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="建议在容器安全配置中修正漏洞 &quot;:::

1. 若要查看和修正特定主机出现故障的 CIS 控制，请选择要调查的主机。 

    > [!TIP]
    > 如果你在 &quot;资产清单&quot; 页上启动并达到此建议，请 select &quot;建议&quot; 页上的 &quot; **执行操作** " 按钮。
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="建议在容器安全配置中修正漏洞 &quot;:::

1. 若要查看和修正特定主机出现故障的 CIS 控制，请选择要调查的主机。 

    > [!TIP]
    > 如果你在 &quot;资产清单&quot; 页上启动并达到此建议，请 select &quot;建议&quot; 页上的 &quot; **执行操作** ":::

    Log Analytics 随即打开，其中包含可运行的自定义操作。 默认自定义查询包括评估的所有失败规则的列表，以及有助于你解决问题的指南。

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="建议在容器安全配置中修正漏洞 &quot;:::

1. 若要查看和修正特定主机出现故障的 CIS 控制，请选择要调查的主机。 

    > [!TIP]
    > 如果你在 &quot;资产清单&quot; 页上启动并达到此建议，请 select &quot;建议&quot; 页上的 &quot; **执行操作** ":::

1. 如有必要，请调整查询参数。

1. 当确定命令适合主机使用时，请选择“运行”。


## <a name="next-steps"></a>后续步骤

Docker 强化只是安全中心容器安全功能的一个方面。 

[在安全中心中](container-security.md)了解更多容器安全性。