---
title: 监视 Azure 安全中心容器的安全性
description: 了解如何从 Azure 安全中心检查容器的安全状况
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 900398a701659bff593df042db16890792e5cffd
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744740"
---
# <a name="monitoring-the-security-of-your-containers"></a>监视容器的安全性

本页说明如何使用 "概念" 部分的[容器安全一文](container-security.md)中所述的容器安全功能。

Azure 安全中心涵盖了容器安全的以下三个方面：

- **漏洞管理**-如果你位于安全中心的标准定价层（请参阅[定价](/azure/security-center/security-center-pricing)），则每次推送新映像时，你都可以扫描基于 ARM 的 Azure 容器注册表。 Scanner （由 Qualys 提供支持）提供了安全中心建议的调查结果。
    有关详细说明，请参阅以下[漏洞的扫描容器注册表](#scanning-your-arm-based-container-registries-for-vulnerabilities)。

- **强化容器的 Docker 主机**-安全中心查找在 IaaS Linux vm 或运行 Docker 的其他 Linux 计算机上托管的非托管容器，并持续将容器的配置与 Internet 安全（CIS） Docker 基准中心进行比较。 如果容器不满足任何控件，安全中心会发出警报。 由于配置错误而持续监视安全风险是任何安全程序的重要组成部分。 
    有关详细说明，请参阅下面的[强化容器的 Docker 主机](#hardening-your-containers-docker-hosts)。

- **强化 Azure Kubernetes 服务群集**-安全中心在 Azure Kubernetes service 群集的配置中发现漏洞时提供了建议。 有关可能出现的具体建议的详细信息，请参阅[Kubernetes 服务建议](recommendations-reference.md#recs-containers)。

- **运行时保护**-如果你位于安全中心的标准定价层，你将获得容器化环境的实时威胁防护。 安全中心为主机和 AKS 群集级别的可疑活动生成警报。 有关可能出现的相关安全警报的详细信息，请参阅警报引用表的[Azure Kubernetes Service 群集](alerts-reference.md#alerts-akscluster)和[容器的警报-主机级别](alerts-reference.md#alerts-containerhost)部分的警报。

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>针对漏洞扫描基于 ARM 的容器注册表 

1. 若要启用 Azure 容器注册表映像的漏洞扫描，请执行以下操作：

    1. 确保你在 Azure 安全中心的标准定价层上。

    1. 在 "**定价 & 设置**" 页上，为订阅启用可选的容器注册表包![：启用容器注册表包](media/monitor-container-security/enabling-container-registries-bundle.png)

        安全中心现已准备好扫描推送到注册表中的映像。 

        >[!NOTE]
        >此功能按映像收费。


1. 若要触发映像扫描，请将其推送到注册表。 

    扫描完成时（通常在大约10分钟后），安全中心建议中提供了结果。
    

1. 若要查看发现，请参阅 "**建议**" 页。 如果发现问题，你将看到以下建议：

    ![解决问题的建议 ](media/monitor-container-security/acr-finding.png)


1. 选择 "建议"。 
    此时会打开 "建议详细信息" 页，其中包含附加信息。 此信息包括包含易受攻击的映像的注册表列表（"受影响的资源"）和修正步骤。 

1. 选择特定的注册表以查看其中包含易受攻击的存储库的存储库。

    ![选择注册表](media/monitor-container-security/acr-finding-select-registry.png)

    此时会打开 "注册表详细信息" 页，其中列出了受影响的存储库。

1. 选择特定的存储库，查看其中包含有漏洞映像的存储库。

    ![选择存储库](media/monitor-container-security/acr-finding-select-repository.png)

    此时将打开 "存储库详细信息" 页。 它列出了易受攻击的映像，并评估了发现的严重性。

1. 选择特定映像以查看漏洞。

    ![选择图像](media/monitor-container-security/acr-finding-select-image.png)

    此时会打开所选图像的结果列表。

    ![发现列表](media/monitor-container-security/acr-findings.png)

1. 若要了解查找的详细信息，请选择 "查找"。 

    此时将打开 "发现详细信息" 窗格。

    [!["结果详细信息" 窗格](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    此窗格包括问题的详细说明，以及指向外部资源的链接，以帮助缓解威胁。

1. 按照此窗格的 "更正" 部分中的步骤进行操作。

1. 执行修正安全问题所需的步骤后，请在注册表中替换映像：

    1. 推送更新后的映像。 这将触发扫描。 
    
    1. 请查看 "建议" 页，以了解建议 "应修正 Azure 容器注册表映像中的漏洞"。 
    
        如果仍显示该建议，并且已处理的图像仍显示在易受攻击的映像列表中，请再次检查修正步骤。

    1. 如果确定已推送、扫描了更新的映像，但未在建议中出现，请从注册表中删除 "旧的" 有漏洞的映像。


## <a name="hardening-your-containers-docker-hosts"></a>强化容器的 Docker 主机

安全中心持续监视 Docker 主机的配置，并生成反映行业标准的安全建议。

查看 Azure 安全中心针对容器的 Docker 主机的安全建议：

1. 在安全中心导航栏中，打开 "**计算 & 应用**"，然后选择 "**容器**" 选项卡。

1. （可选）将容器资源列表筛选为容器主机主机。

    ![容器资源筛选器](media/monitor-container-security/container-resources-filter.png)

1. 从容器主机计算机列表中，选择一个进行进一步调查。

    ![容器主机推荐](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    此时会打开 "**容器主机信息" 页**，其中包含主机的详细信息和建议列表。

1. 从 "建议" 列表中，选择要进一步调查的建议。

    ![容器主机推荐列表](media/monitor-container-security/container-host-rec.png)

1. 还可以阅读说明、信息、威胁和修正步骤。 

1. 选择页面底部的 "**执行操作**"。

    [!["采取操作" 按钮](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics 随即打开，其中包含可运行的自定义操作。 默认的自定义查询包含已评估的所有失败的规则的列表，以及帮助你解决问题的指南。

    [![Log Analytics 操作](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. 如有必要，请调整查询参数。

1. 如果确定命令适用于主机，请选择 "**运行**"。



## <a name="next-steps"></a>后续步骤

本文介绍了如何使用安全中心的容器安全功能。 

有关其他相关材料，请参阅以下页面： 

- [容器安全中心建议](recommendations-reference.md#recs-containers)
- [AKS 群集级别的警报](alerts-reference.md#alerts-akscluster)
- [容器主机级别的警报](alerts-reference.md#alerts-containerhost)
