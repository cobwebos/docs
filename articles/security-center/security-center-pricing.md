---
title: Azure 安全中心定价
description: Azure 安全中心提供两种模式，分别随附和不随附 Azure Defender。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: a01d4137217aa594d4636f3338d3f33dc03cc836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91713735"
---
# <a name="pricing-of-azure-security-center"></a>Azure 安全中心定价
Azure 安全中心为 Azure、本地和其他云中运行的工作负载提供统一的安全管理和高级威胁防护功能。 它可以提供针对混合云工作负载的可见性和可控性、可减小在威胁下的曝光面的积极防御功能以及有助于随时响应快速演变的网络攻击的智能检测功能。


## <a name="free-option-vs-azure-defender-enabled"></a>“免费选项”和“启用 Azure Defender”

安全中心提供两种模式：

- **Azure Defender 关闭**（免费）- 当你首次在 Azure 门户中访问 Azure 安全中心仪表板时，或通过 API 以编程方式启用后，会在你的所有 Azure 订阅上免费启用未启用 Azure Defender 的安全中心。 此免费模式可提供安全策略、持续的安全评估和切实可行的安全建议来帮助你保护 Azure 资源。

- **Azure Defender 打开** - 启用 Azure Defender 后，会将免费模式的功能扩展到私有云和其他公有云中运行的工作负载，并在混合云工作负载中提供了统一的安全管理和威胁防护。 Azure Defender 的一些主要功能：

    - **** 混合安全 – 在所有本地和云工作负载上获得统一的安全视图。 应用安全策略并持续评估混合云工作负载的安全性，确保符合安全标准。 收集、搜索并分析来自多个源（包括防火墙和其他合作伙伴解决方案）的安全数据。
    - **威胁防护警报 ** - 高级行为分析和 Microsoft Intelligent Security Graph 针对不断演变的网络攻击提供防护边界。 利用内置行为分析和机器学习来识别攻击和零时差攻击。 监视网络、计算机和云服务是否出现有即将来袭的攻击和攻破后活动。 使用交互工具和上下文威胁智能简化调查。
    - **虚拟机和容器注册表的漏洞扫描** - 轻松将扫描程序部署到所有虚拟机，这是业界最先进的漏洞管理解决方案。 直接在安全中心内查看、调查和修复扫描到的漏洞。 
    - **访问和应用程序控件** - 通过应用适合特定工作负载且由机器学习提供支持的建议来创建允许和拒绝列表，阻止恶意软件和其他不需要的应用程序。 实时减小网络受攻击面，控制对 Azure VM 上的管理端口的访问。 这会显著减小在暴力和其他网络攻击下的曝光面。
    - **容器安全功能** - 获得在容器化环境中进行漏洞管理和实时威胁保护的好处。 启用**适用于容器注册表的 Azure Defender** 时，等待全部功能启用完毕可能需要最多 12 个小时。 根据推送到已连接注册表的唯一容器映像的数量收费。 一个映像经过一次扫描后，不会再对其收取相关费用，除非再次对其进行了修改和推送。 

## <a name="try-azure-defender-free-for-30-days"></a>免费试用 Azure Defender 30 天

Azure Defender 前 30 天免费。 30 天后，如果选择继续使用服务，我们会自动开始收取使用费用。

## <a name="enable-azure-defender"></a>启用 Azure Defender

可以使用 Azure Defender 保护整个 Azure 订阅，且该保护将由订阅中的所有资源继承。

启用 Azure Defender：

1. 从安全中心的主菜单中，选择“定价和设置”****。
1. 选择要升级的订阅。
1. 选择要升级的“Azure Defender 打开”****。
1. 选择“保存”。****

下面是一个示例订阅的定价页。 可以看到，Azure Defender 中的每个计划是单独定价的，并可以单独设置为“打开”或“关闭”。

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="门户中安全中心的定价页":::

> [!NOTE]
> 若要启用包括威胁防护功能在内的全部安全中心功能，必须在包含适用工作负载的订阅上启用 Azure Defender。 如果在工作区级别启用它，则不会为 Azure 资源启用实时 VM 访问、自适应应用程序控制和网络检测功能。 此外，只在工作区级别提供两种 Azure Defender 计划：适用于服务器的 Azure Defender，以及适用于计算机上的 SQL Server 的 Azure Defender。
>
> 可以在订阅级别或资源级别启用**用于存储的 Azure Defender 帐户**。
> 可以在订阅级别或资源级别启用**用于 SQL 的 Azure Defender**。
> 只能在资源级别为 **Azure Database for MariaDB/MySQL/PostgreSQL** 启用威胁防护。


## <a name="next-steps"></a>后续步骤
本文介绍了安全中心定价。 如需查看相关材料，请参阅：

- [如何优化 Azure 工作负载成本](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [根据所选货币和所在区域的定价详细信息](https://azure.microsoft.com/pricing/details/security-center/)
- 你可能希望管理成本，并通过将解决方案的应用范围限制为特定的一组代理来限制为解决方案收集的数据量。 [解决方案目标](../operations-management-suite/operations-management-suite-solution-targeting.md)使用户可以向解决方案应用一个范围，并可将目标设定为工作区中的一个计算机子集。 如果使用解决方案目标功能，安全中心会将工作区列为没有解决方案。