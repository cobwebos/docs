---
title: Azure 管理解决方案 | Microsoft Docs
description: 管理解决方案包括 Azure 中打包的管理方案，客户可将其添加到 Log Analytics 工作区。  本文将详细介绍如何由客户及合作伙伴创建自定义解决方案。
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d46b869815fef44a8137bb5121133a1c0140ca30
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29394249"
---
# <a name="working-with-management-solutions-in-azure-preview"></a>使用 Azure 中的管理解决方案（预览）
> [!NOTE]
> 这是 Azure 中的管理解决方案（当前处于预览状态）的初步文档。    
> 
> 

管理解决方案包括打包的管理方案，客户可将其添加到 Azure 环境。  除了[由 Microsoft 提供的解决方案](../log-analytics/log-analytics-add-solutions.md)，合作伙伴和客户还可以创建要在各自环境中使用或可通过社区提供给客户的管理解决方案。

## <a name="finding-and-installing-management-solutions"></a>查找和安装管理解决方案
可以使用多种方法查找和安装管理解决方案，如以下各部分中所述。

### <a name="azure-marketplace"></a>Azure Marketplace
可以从 Azure 门户的 Azure Marketplace 安装由 Microsoft 和受信任合作伙伴提供的管理解决方案。

1. 登录到 Azure 门户。
2. 在左窗格中，选择“所有服务”。
3. 向下滚动到“**解决方案**”，或者在“**筛选器**”对话框中键入 *solutions*。
4. 单击“**+ 添加**”按钮。
5. 若要搜索感兴趣的解决方案，请浏览，单击“筛选器”按钮，或在“搜索所有内容”框中输入。
6. 单击要查看其详细信息的应用商店项。
7. 单击“**创建**”，以打开“**添加解决方案**”窗格。
8. 除了解决方案中任何参数的值，系统还会提示提供所需的信息，例如 [Log Analytics 工作区和自动化帐户](#log-analytics-workspace-and-automation-account)。
9. 单击“**创建**”可安装解决方案。

### <a name="oms-portal"></a>OMS 门户
可以从 OMS 门户的解决方案库中安装由 Microsoft 提供的管理解决方案。

1. 登录到 OMS 门户。
2. 单击“**解决方案库**”磁贴。
3. 在 OMS“解决方案库”页面上，了解有关每个可用解决方案的信息。 单击要添加的解决方案的名称。
4. 在选择的解决方案页面上，会显示有关该解决方案的详细信息。 单击 **“添加”**。
5. 添加的解决方案的新磁贴显示在门户的“概述”页面上，Log Analytics 处理数据后，便可以开始使用该解决方案。

### <a name="azure-quickstart-templates"></a>Azure 快速入门模板
社区成员可以将管理解决方案提交到 Azure 快速入门模板。  可以下载这些模板供以后安装，或进行检查以了解如何[创建自己的解决方案](#creating-a-solution)。

1. 请遵循 [Log Analytics 工作区和自动化帐户](#log-analytics-workspace-and-automation-account)中所述的过程来链接工作区和帐户。
2. 转到 [Azure 快速入门模板](https://azure.microsoft.com/documentation/templates/)。  
3. 搜索感兴趣的解决方案。
4. 从结果中选择解决方案以查看其详细信息。
5. 单击“**部署到 Azure**”按钮。
6. 除了解决方案中任何参数的值，系统还会提示提供资源组和位置等信息。
7. 单击“**购买**”可安装解决方案。

### <a name="deploy-azure-resource-manager-template"></a>部署 Azure 资源管理器模板
对于从社区获取或[自己创建](#creating-a-solution)的解决方案，将作为 Resource Manager 模板实施，并且可以使用任何标准方法[部署模板](../azure-resource-manager/resource-group-template-deploy-portal.md)。  请注意，在安装解决方案之前，必须创建并链接 [Log Analytics 工作区和自动化帐户](#log-analytics-workspace-and-automation-account)。

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics 工作区和自动化帐户
大多数管理解决方案都需要 [Log Analytics 工作区](../log-analytics/log-analytics-manage-access.md)来包含视图，也需要[自动化帐户](../automation/automation-security-overview.md#automation-account-overview)来包含 runbook 和相关资源。 工作区和帐户必须满足以下要求。

* 一种解决方案只能使用一个 Log Analytics 工作区和一个自动化帐户。  
* 解决方案所使用的 Log Analytics 工作区和自动化帐户必须相互进行链接。 一个 Log Analytics 工作区只能链接到一个自动化帐户，而一个自动化帐户也只能链接到一个 Log Analytics 工作区。
* 若要进行链接，Log Analytics 工作区和自动化帐户必须位于相同的资源组和区域中。  美国东部地区的 Log Analytics 工作区以及美国东部 2 的自动化帐户除外。

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>在 Log Analytics 工作区和自动化帐户之间创建链接
如何指定 Log Analytics 工作区和自动化帐户取决于解决方案的安装方法。

* 通过 OMS 门户安装 Microsoft 解决方案时，该解决方案安装在当前 Log Analytics 工作区中，不需要任何自动化帐户。
* 通过 Azure Marketplace 安装解决方案时，系统会提示提供一个 Log Analytics 工作区和自动化帐户，并在它们之间创建一个链接。  
* 对于 Azure Marketplace 外的解决方案，必须在安装解决方案之前链接 Log Analytics 工作区和自动化帐户。  为此，可以在 Azure Marketplace 中选择任何解决方案，并选择 Log Analytics 工作区和自动化帐户。  无需实际安装解决方案，因为只要选择了 Log Analytics 工作区和自动化帐户，就会创建链接。  创建链接后，可以对任何解决方案使用该 Log Analytics 工作区和自动化帐户。 

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>验证 Log Analytics 工作区和自动化帐户之间的链接
可以使用以下过程验证 Log Analytics 工作区和自动化帐户之间的链接。

1. 在 Azure 门户中选择自动化帐户。
2. 如果已启用菜单的“相关资源”部分中的“工作区”设置，则此帐户将附加到 Log Analytics 工作区。  可单击“工作区”查看工作区的详细信息。

## <a name="listing-management-solutions"></a>列出管理解决方案
使用以下过程查看链接到 Azure 订阅的工作区中的管理解决方案。

1. 登录到 Azure 门户。
2. 在左窗格中，选择“所有服务”。
3. 向下滚动到“**解决方案**”，或者在“**筛选器**”对话框中键入 *solutions*。
4. 将列出安装在所有工作区中的解决方案。

请注意，使用 OMS 门户仅可以查看在当前工作区中安装的 Microsoft 解决方案。

## <a name="removing-a-management-solution"></a>删除管理解决方案
删除管理解决方案时，也会删除该解决方案中的所有资源。  

1. 使用[列出解决方案](#listing-solutions)中的过程在 Azure 门户中查找解决方案。
2. 选择要删除的解决方案。
3. 单击“删除” 按钮。

## <a name="creating-a-management-solution"></a>创建管理解决方案
有关创建管理解决方案的完整指南在 [在 Operations Management Suite (OMS) 中创建解决方案](operations-management-suite-solutions-creating.md)中提供。 

## <a name="next-steps"></a>后续步骤
* 在 [Azure 快速启动模板](https://azure.microsoft.com/documentation/templates)中搜索不同 Resource Manager 模板的示例。
* 创建自己的[管理解决方案](operations-management-suite-solutions-creating.md)。

