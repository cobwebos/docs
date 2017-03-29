---
title: "Operations Management Suite (OMS) 中的解决方案 | Microsoft 文档"
description: "通过提供客户可添加到其 OMS 工作区的打包管理方案，解决方案可扩展 Operations Management Suite (OMS) 的功能。  本文将详细介绍如何由客户及合作伙伴创建自定义解决方案。"
services: operations-management-suite
documentationcenter: 
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
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 2443dd73fdf441721bd6f6f340da515d9f5a22a2
ms.lasthandoff: 03/22/2017


---
# <a name="working-with-management-solutions-in-operations-management-suite-oms-preview"></a>在 Operations Management Suite (OMS) 中使用管理解决方案（预览版）
> [!NOTE]
> 这是在 OMS 中管理解决方案的初步文档，当前仅提供预览版。    
> 
> 

通过提供客户可添加到其环境的打包管理方案，管理解决方案可扩展 Operations Management Suite (OMS) 的功能。  除了[由 Microsoft 提供的解决方案](../log-analytics/log-analytics-add-solutions.md)，合作伙伴和客户还可以创建要在各自环境中使用或可通过社区提供给客户的管理解决方案。

## <a name="finding-and-installing-management-solutions"></a>查找和安装管理解决方案
可以使用多种方法查找和安装管理解决方案，如以下各部分中所述。

### <a name="azure-marketplace"></a>Azure 应用商店
可以从 Azure 门户的 Azure 应用商店安装由 Microsoft 和受信任合作伙伴提供的管理解决方案。

1. 登录到 Azure 门户。
2. 在左窗格中，选择“**更多服务**”。
3. 向下滚动到“**解决方案**”，或者在“**筛选器**”对话框中键入 *solutions*。
4. 单击“**+ 添加**”按钮。
5. 要搜索你感兴趣的解决方案，请浏览，单击“**筛选器**”按钮，或在“**搜索所有内容**”框中键入。
6. 单击要查看其详细信息的应用商店项。
7. 单击“**创建**”，以打开“**添加解决方案**”窗格。
8. 除了解决方案中任何参数的值，系统还会提示你提供所需的信息，例如 [OMS 工作区和自动化帐户](#oms-workspace-and-automation-account)。
9. 单击“**创建**”可安装解决方案。

### <a name="oms-portal"></a>OMS 门户
可以从 OMS 门户的解决方案库中安装由 Microsoft 提供的管理解决方案。

1. 登录到 OMS 门户。
2. 单击“**解决方案库**”磁贴。
3. 在 OMS“解决方案库”页面上，了解有关每个可用解决方案的信息。 单击要添加到 OMS 的解决方案的名称。
4. 在选择的解决方案页面上，会显示有关该解决方案的详细信息。 单击 **“添加”**。
5. 添加的解决方案的新磁贴显示在 OMS 的“概述”页面上，OMS 服务处理你的数据后，你就可以开始使用该解决方案。

### <a name="azure-quickstart-templates"></a>Azure 快速入门模板
社区成员可以将管理解决方案提交到 Azure 快速入门模板。  可以下载这些模板供以后安装，或进行检查以了解如何[创建自己的解决方案](#creating-a-solution)。

1. 请遵循 [OMS 工作区和自动化帐户](#oms-workspace-and-automation-account)中所述的过程来链接工作区和帐户。
2. 转到 [Azure 快速入门模板](https://azure.microsoft.com/documentation/templates/)。  
3. 搜索你感兴趣的解决方案。
4. 从结果中选择解决方案以查看其详细信息。
5. 单击“**部署到 Azure**”按钮。
6. 除了解决方案中任何参数的值，系统还会提示你提供资源组和位置等信息。
7. 单击“**购买**”可安装解决方案。

### <a name="deploy-azure-resource-manager-template"></a>部署 Azure Resource Manager 模板
对于从社区获取或[自己创建](#creating-a-solution)的解决方案，将作为 Resource Manager 模板实施，并且可以使用任何标准方法[部署模板](../azure-resource-manager/resource-group-template-deploy-portal.md)。  请注意，在安装解决方案之前，必须创建并链接 [OMS 工作区和自动化帐户](#oms-workspace-and-automation-account)。

## <a name="oms-workspace-and-automation-account"></a>OMS 工作区和自动化帐户
大多数管理解决方案需要 [OMS 工作区](../log-analytics/log-analytics-manage-access.md)来包含视图，也需要[自动化帐户](../automation/automation-security-overview.md#automation-account-overview)来包含 runbook 和相关资源。 工作区和帐户必须满足以下要求。

* 一种解决方案只能使用一个 OMS 工作区和一个自动化帐户。  
* 解决方案所使用的 OMS 工作区和自动化帐户必须链接到另一个。 OMS 工作区只能链接到一个自动化帐户，且自动化帐户也只能链接到一个 OMS 工作区。
* 若要链接，OMS 工作区和自动化帐户必须位于相同的资源组和区域中。  美国东部地区的 OMS 工作区以及美国东部 2 的自动化帐户除外。

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>在 OMS 工作区和自动化帐户之间创建链接
如何指定 OMS 工作区和自动化帐户取决于解决方案的安装方法。

* 通过 OMS 门户安装 Microsoft 解决方案时，将其安装在当前 OMS 工作区中，不需要任何自动化帐户。
* 通过 Azure 应用商店安装解决方案时，系统会提示你提供一个 OMS 工作区和自动化帐户，并为你创建它们之间的链接。  
* 对于 Azure 应用商店外的解决方案，你必须在安装解决方案之前链接 OMS 工作区和自动化帐户。  为此，可以在 Azure 应用商店中选择任何解决方案，并选择 OMS 工作区和自动化帐户。  无需实际安装解决方案，因为只要选择了 OMS 工作区和自动化帐户，就会创建链接。  创建链接后，可以对任何解决方案使用该 OMS 工作区和自动化帐户。 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>验证 OMS 工作区和自动化帐户之间的链接
可以使用以下过程验证 OMS 工作区和自动化帐户之间的链接。

1. 在 Azure 门户中选择自动化帐户。
2. 滚动到“**设置**”窗格底部。
3. 如果“**设置**”窗格中有名为“**OMS 资源**”的部分，则此帐户连接到 OMS 工作区。
4. 选择“**工作区**”，以查看链接到此自动化帐户的 OMS 工作区的详细信息。

## <a name="listing-management-solutions"></a>列出管理解决方案
使用以下过程查看链接到 Azure 订阅的工作区中的管理解决方案。

1. 登录到 Azure 门户。
2. 在左窗格中，选择“**更多服务**”。
3. 向下滚动到“**解决方案**”，或者在“**筛选器**”对话框中键入 *solutions*。
4. 将列出安装在所有工作区中的解决方案。

请注意，使用 OMS 门户仅可以查看在当前工作区中安装的 Microsoft 解决方案。

## <a name="removing-a-management-solution"></a>删除管理解决方案
删除管理解决方案时，也会删除该解决方案中的所有资源。  

1. 使用[列出解决方案](#listing-solutions)中的过程在 Azure 门户中查找解决方案。
2. 选择你想要删除的解决方案。
3. 单击“删除” 按钮。

## <a name="creating-a-management-solution"></a>创建管理解决方案
有关创建管理解决方案的完整指南在 [在 Operations Management Suite (OMS) 中创建解决方案](operations-management-suite-solutions-creating.md)中提供。 

## <a name="next-steps"></a>后续步骤
* 在 [Azure 快速启动模板](https://azure.microsoft.com/documentation/templates)中搜索不同 Resource Manager 模板的示例。
* 创建自己的[管理解决方案](operations-management-suite-solutions-creating.md)。


