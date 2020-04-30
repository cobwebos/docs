---
title: 在 Azure 安全中心集成安全解决方案 | Microsoft Docs
description: 了解如何将 Azure 安全中心与合作伙伴集成，以增强 Azure 资源的总体安全性。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: 48869140ba8cd1a9598562b0057b0005d8fcd9c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758027"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>在 Azure 安全中心集成安全解决方案
本文档介绍如何管理已连接到 Azure 安全中心的安全解决方案，以及如何添加新的安全解决方案。

## <a name="integrated-azure-security-solutions"></a>集成式 Azure 安全解决方案
可以通过安全中心轻松地在 Azure 中启用集成式安全解决方案。 优势包括：

- **简化部署**：安全中心提供对集成式合作伙伴解决方案的简化预配。 对于反恶意软件和漏洞评估等解决方案，安全中心可以在虚拟机上设置代理。 对于防火墙设备，安全中心可以负责处理所需的大量网络配置。
- **集成检测**：自动收集、聚合合作伙伴解决方案中的安全事件，并将其作为安全中心警报和事件的一部分显示。 这些事件还与来自其他源的检测融合在一起，以提供高级威胁检测功能。
- **统一运行状况监视和管理**：利用集成运行状况事件，客户可以一目了然地监视所有合作伙伴解决方案。 可通过使用合作伙伴解决方案轻松地访问高级设置，进行基本管理。

目前，集成的安全解决方案包括通过[Qualys](https://www.qualys.com/public-cloud/#azure)和[Rapid7](https://www.rapid7.com/products/insightvm/)和 Microsoft 应用程序网关 Web 应用程序防火墙的漏洞评估。

> [!NOTE]
> 安全中心不会将 Log Analytics 代理安装在合作伙伴虚拟设备上，因为大多数安全供应商都禁止在其设备上运行外部代理。

若要详细了解 Qualys 中的漏洞扫描工具的集成，包括适用于标准层客户的内置扫描程序，请参阅： 

- [虚拟机的集成漏洞扫描程序](built-in-vulnerability-assessment.md)。
- [部署合作伙伴漏洞扫描解决方案](partner-vulnerability-assessment.md)。

安全中心还提供的漏洞分析：

* SQL 数据库-请参阅[漏洞评估仪表板中的探索漏洞评估报告](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports)
* Azure 容器注册表映像-请参阅[Azure 容器注册表与安全中心的集成（预览版）](azure-container-registry-integration.md)

## <a name="how-security-solutions-are-integrated"></a>安全中心如何集成
从安全中心部署的 Azure 安全解决方案是自动连接的。 你还可以连接其他安全数据源，包括在本地或其他云中运行的计算机。

[![合作伙伴解决方案集成](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>管理集成式 Azure 安全解决方案和其他数据源

1. 在[Azure 门户](https://azure.microsoft.com/features/azure-portal/)中，打开 "**安全中心**"。

1. 从安全中心的菜单中，选择 "**安全解决方案**"。

在 "**安全解决方案**" 页中，可以查看集成的 Azure 安全解决方案的运行状况，并运行基本的管理任务。

### <a name="connected-solutions"></a>已连接解决方案

"**已连接解决方案**" 部分包含当前连接到安全中心的安全解决方案。 它还显示每个解决方案的运行状况状态。  

![已连接解决方案](./media/security-center-partner-integration/connected-solutions.png)

合作伙伴解决方案的状态可能为：

* **正常**（绿色）-没有运行状况问题。
* 不**正常**（红色）-存在需要立即关注的运行状况问题。
* **已停止报告**（橙色）-解决方案已停止报告其运行状况。
* **未报告**（灰色）-解决方案尚未报告任何内容，并且没有可用的运行状况数据。 如果某个解决方案刚刚连接并且仍在部署，则该解决方案的状态可能未报告。

> [!NOTE]
> 如果没有运行状况数据可用，则安全中心会显示上次收到事件的日期和时间以指示解决方案是否正在报告。 如果没有运行状况数据，并且在过去14天内未收到任何警报，则安全中心会指示该解决方案不正常或未进行报告。
>
>

选择 "**查看**" 以获取其他信息和选项，例如：

   - **解决方案控制台**-打开此解决方案的管理体验。
   - **链接 VM** -打开 "链接应用程序" 页。 此处，可将资源连接到合作伙伴解决方案。
   - **删除解决方案**
   - **配置**

   ![合作伙伴解决方案详细信息](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>已发现解决方案

安全中心会自动发现在 Azure 中运行但未连接到安全中心的安全解决方案，并显示 "**发现的解决方案**" 部分中的解决方案。 这些解决方案包括 Azure 解决方案，例如[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)和合作伙伴解决方案。

> [!NOTE]
> 在订阅级别，安全中心标准层是已发现解决方案功能所必需的。 有关定价层的详细信息，请参阅[定价](security-center-pricing.md)。
>

在解决方案下选择 "**连接**"，以与安全中心集成并通知安全警报。

### <a name="add-data-sources"></a>添加数据源

****“添加数据源”部分包括其他可以连接的可用数据源。 如需从任何此类源添加数据的说明，请单击“添加”。****

![数据源](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>后续步骤

本文介绍了如何在安全中心集成合作伙伴的解决方案。 有关更多信息，请参阅以下文章：

* [导出安全警报和建议](continuous-export.md)。 了解如何设置与 Azure Sentinel 或任何其他 SIEM 的集成。
* [在安全中心进行安全运行状况监视](security-center-monitoring.md)。 了解如何监视 Azure 资源的运行状况。