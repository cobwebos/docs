---
title: 如何创建 Azure 支持请求 | Microsoft Docs
description: 需要帮助的客户可以使用 Azure 门户来查找自助服务解决方案以及创建和管理支持请求。
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 01/23/2020
ms.author: kfollis
ms.openlocfilehash: 28a29bbb94f63657a69e873bb8f969e96ee92c76
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248484"
---
# <a name="how-to-create-an-azure-support-request"></a>如何创建 Azure 支持请求

## <a name="overview"></a>概述

Azure 使你能够创建和管理支持请求，也称为支持票证。 你可以创建和管理[Azure 门户](https://portal.azure.com)中的请求，本文将对此进行介绍。 你还可以使用[Azure 支持票证 REST API](/rest/api/support)以编程方式创建和管理请求。

> [!NOTE]
> Azure 门户 URL 特定于部署组织的 Azure 云。
>
>* 用于商业用途的 Azure 门户是： [https://portal.azure.com](https://portal.azure.com)
>* 德国 Azure 门户是： [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* 美国政府 Azure 门户为： [https://portal.azure.us](https://portal.azure.us)
>
>

根据客户反馈，我们更新了支持请求体验，重点介绍三个主要目标：

* **简化**：轻松查找并简化如何提交支持请求。
* **集成**：若要解决 Azure 资源的问题，但不切换上下文，可以轻松地提出支持请求。
* **高效**：收集支持代理高效解决问题所需的关键信息。

## <a name="getting-started"></a>入门

可以在 Azure 门户获取**帮助和支持**。 它可从 "Azure 门户" 菜单、"全局" 标头或服务的 "资源" 菜单中获取。 你必须具有相应的权限，然后才能发出支持请求。

### <a name="role-based-access-control"></a>基于角色的访问控制

若要创建支持请求，你必须是管理员或在订阅级别分配到 "[支持请求参与者](../../role-based-access-control/built-in-roles.md#support-request-contributor)" 角色。

### <a name="go-to-help--support-from-the-global-header"></a>从全局标头中转到 "帮助 + 支持"

若要从 Azure 门户中的任何位置启动支持请求：

1. 选择 **？** 在全局标头中。 然后选择 "**帮助 + 支持**"。

   ![帮助和支持](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. 选择“新建支持请求”。 按照提示向我们提供有关你的问题的信息。 我们将建议一些可能的解决方案，收集有关问题的详细信息，并帮助你提交和跟踪支持请求。

   ![新建支持请求](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>从资源菜单中转到 "帮助 + 支持"

若要在资源的上下文中启动支持请求，当前正在使用：

1. 从资源菜单的 "**支持 + 故障排除**" 部分中，选择 "**新建支持请求**"。

   ![在上下文中](./media/how-to-create-azure-support-request/incontext2lower.png)

2. 按照提示向我们提供有关你遇到的问题的信息。 当你从资源中启动支持请求过程时，将为你预先选择一些选项。

## <a name="create-a-support-request"></a>创建支持请求

我们将引导你完成一些步骤，收集有关你的问题的信息并帮助你解决问题。 以下各部分介绍了每个步骤。

### <a name="basics"></a>基础

支持请求过程的第一步是收集关于问题和支持计划的基本信息。

在 "**新支持请求**" 的 "**基本**信息" 选项卡上，使用选择器开始告诉我们问题。 首先，您将确定问题类型的一些常规类别，并选择相关订阅。 选择服务，例如**运行 Windows 的虚拟机**。 选择资源，如虚拟机的名称。 用自己的字词描述问题，然后**选择 "问题类型**" 以获取更具体的信息。

![“基本信息”边栏选项卡](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure 为订阅管理提供无限支持，包括计费、配额调整和帐户传输。 对于技术支持，需要支持计划。 [了解关于支持计划的详细信息](https://azure.microsoft.com/support/plans)。
>
>

### <a name="solutions"></a>解决方案

收集基本信息后，我们将向您展示要尝试自己的解决方案。 在某些情况下，我们甚至可以运行快速诊断。 解决方案由 Azure 工程师编写，可解决最常见的问题。

### <a name="details"></a>详细信息

接下来，我们收集有关问题的其他详细信息。 在此步骤中提供详尽的详细信息可帮助我们将你的支持请求路由到正确的代理。

如果可能，请告诉我们问题的开始时间，以及重现该问题的任何步骤。 可以上传文件，如日志文件或诊断输出。

获取有关问题的所有信息后，请选择 "如何获取支持"。 在 "**详细信息**" 的 "**支持方法**" 部分中，选择影响的严重性。 提供首选联系方法、与你联系的良好时间以及你的支持语言。

接下来，请填写 "**联系信息**" 部分，以便我们知道如何与你联系。

### <a name="review--create"></a>查看 + 创建

完成每个选项卡上的所有必需信息，然后选择 "**查看 + 创建**"。 检查将发送以支持的详细信息。 返回到任何选项卡以在需要时进行更改。 当你满足支持请求完成后，请选择 "**创建**"。

支持代理将使用你指定的方法与你联系。 有关初始响应时间的信息，请参阅[支持范围和响应能力](https://azure.microsoft.com/support/plans/response/)。

## <a name="all-support-requests"></a>所有支持请求

可以通过转到 "**帮助 + 支持**" >  **所有支持请求**来查看支持请求的详细信息和状态。

![所有支持请求](./media/how-to-create-azure-support-request/allrequestslower.png)

在此页上，可以按**订阅**、**创建**日期（UTC）和**状态**筛选支持请求。 另外，还可以在此页面上对支持请求进行排序和搜索操作。

选择支持请求以查看详细信息，包括严重性和支持代理响应所需的时间。

如果要更改请求的严重性，请选择 "**业务影响**"。 从严重性列表中选择要分配的严重性。

> [!NOTE]
> 最大严重性级别取决于支持计划。 [了解关于支持计划的详细信息](https://azure.microsoft.com/support/plans)。
>
>
若要了解有关 Azure 中自助支持选项的详细信息，请观看此视频：

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>后续步骤

* [向我们发送你的反馈和建议](https://feedback.azure.com/forums/266794-support-feedback)
* 与我们一起参与[Twitter](https://twitter.com/azuresupport)
* 在[MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure)中获取对等方的帮助
* 有关详细信息，请参阅[Azure 支持常见问题](https://azure.microsoft.com/support/faq)
