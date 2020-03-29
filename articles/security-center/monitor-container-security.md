---
title: 监视 Azure 安全中心中容器的安全性
description: 了解如何从 Azure 安全中心检查容器的安全状况
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919526"
---
# <a name="monitoring-the-security-of-your-containers"></a>监控容器的安全性

本页介绍如何使用["容器安全"一文中所述的容器](container-security.md)安全功能。

Azure 安全中心涵盖容器安全的以下三个方面：

- **漏洞管理**- 如果您位于安全中心的标准定价层（请参阅[定价](/azure/security-center/security-center-pricing)），则可以在每次推送新映像时扫描基于 ARM 的 Azure 容器注册表。 扫描仪（由 Qualys 提供支持）将发现作为安全中心的建议。
    有关详细说明，请参阅[扫描下面的容器注册表中的漏洞](#scanning-your-arm-based-container-registries-for-vulnerabilities)。

- **强化容器的 Docker 主机**- 安全中心发现托管在 IaaS Linux VM 或其他运行 Docker 的 Linux 计算机上的非托管容器，并持续将容器的配置与 Internet 安全中心 （CIS） Docker 基准进行比较。 如果容器不符合任何控件，安全中心会提醒您。 持续监控配置不当造成的安全风险是任何安全计划的关键组成部分。 
    有关详细说明，请参阅[在下面强化容器的 Docker 主机](#hardening-your-containers-docker-hosts)。

- **强化 Azure 库伯奈斯服务群集**- 安全中心在发现 Azure 库伯内斯服务群集配置中的漏洞时提供建议。 有关可能显示的具体建议的详细信息，请参阅[库伯内斯服务建议](recommendations-reference.md#recs-containers)。

- **运行时保护**- 如果您位于安全中心的标准定价层，您将获得容器化环境的实时威胁保护。 安全中心在主机和 AKS 群集级别生成可疑活动的警报。 有关可能出现的相关安全警报的详细信息，请参阅[Azure Kubernetes 服务群集的警报](alerts-reference.md#alerts-akscluster)和[容器的警报 -](alerts-reference.md#alerts-containerhost)警报参考表的主机级别部分。

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>扫描基于 ARM 的容器注册表中的漏洞 

1. 要启用 Azure 容器注册表映像的漏洞扫描：

    1. 确保您位于 Azure 安全中心的标准定价层。

    1. 在**定价&设置**页中，为订阅启用可选的容器注册表包：![启用容器注册表包](media/monitor-container-security/enabling-container-registries-bundle.png)

        安全中心现在可以扫描推送到注册表的图像。 

        >[!NOTE]
        >此功能按图像收费。


1. 要触发映像的扫描，请将其推送到注册表。 

    扫描完成后（通常在大约 10 分钟后），安全中心建议中提供了调查结果。
    

1. 要查看调查结果，请访问 **"建议"** 页面。 如果发现问题，您将看到以下建议：

    ![修复问题的建议 ](media/monitor-container-security/acr-finding.png)


1. 选择建议。 
    建议详细信息页将打开，并包含其他信息。 此信息包括具有易受攻击映像（"受影响的资源"）的注册表的列表以及修正步骤。 

1. 选择特定的注册表以查看其中具有易受攻击存储库的存储库。

    ![选择注册表](media/monitor-container-security/acr-finding-select-registry.png)

    注册表详细信息页将打开，并列出受影响的存储库。

1. 选择特定存储库以查看其中具有易受攻击映像的存储库。

    ![选择存储库](media/monitor-container-security/acr-finding-select-repository.png)

    将打开存储库详细信息页。 它列出了易受攻击的图像以及对调查结果严重性的评估。

1. 选择特定图像以查看漏洞。

    ![选择图像](media/monitor-container-security/acr-finding-select-image.png)

    将打开所选图像的发现列表。

    ![调查结果列表](media/monitor-container-security/acr-findings.png)

1. 要了解有关查找的更多详细信息，请选择该查找。 

    将打开"调查结果详细信息"窗格。

    [![调查结果详细信息窗格](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    此窗格包括问题的详细说明和指向外部资源的链接，以帮助缓解威胁。

1. 按照此窗格的修正部分中的步骤操作。

1. 执行修复安全问题所需的步骤后，请替换注册表中的映像：

    1. 推送更新的图像。 这将触发扫描。 
    
    1. 请查看建议页面，了解"应修复 Azure 容器注册表映像中的漏洞"的建议。 
    
        如果建议仍然显示，并且您处理的图像仍显示在易受攻击图像列表中，请再次检查修正步骤。

    1. 当您确定已推送、扫描更新的图像且不再显示在建议中时，请从注册表中删除"旧"易受攻击的图像。


## <a name="hardening-your-containers-docker-hosts"></a>加固容器的 Docker 主机

安全中心不断监视 Docker 主机的配置，并生成反映行业标准的安全建议。

要查看容器的 Docker 主机的 Azure 安全中心安全建议，

1. 在"安全中心"导航栏中，打开 **"计算&应用**并选择 **"容器**"选项卡。

1. 可以选择将容器资源列表筛选到容器主机。

    ![容器资源筛选器](media/monitor-container-security/container-resources-filter.png)

1. 从容器主机列表中，选择一台以进一步调查。

    ![容器主机建议](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    将打开 **"容器主机信息"页**，其中包含主机的详细信息和建议列表。

1. 从建议列表中，选择一个建议以进一步调查。

    ![容器主机建议列表](media/monitor-container-security/container-host-rec.png)

1. 或者，请阅读说明、信息、威胁和补救步骤。 

1. 选择页面底部的 **"执行操作**"。

    [![执行操作按钮](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    日志分析在打开时，自定义操作可供运行。 默认自定义查询包括评估的所有失败规则的列表，以及帮助您解决问题的准则。

    [![日志分析操作](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. 调整查询参数，并在确定主机准备就绪时选择 **"运行**"。 



## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何使用安全中心的容器安全功能。 

有关其他相关材料，请参阅以下页面： 

- [容器安全中心建议](recommendations-reference.md#recs-containers)
- [AKS 群集级别的警报](alerts-reference.md#alerts-akscluster)
- [容器主机级别的警报](alerts-reference.md#alerts-containerhost)
