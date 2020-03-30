---
title: 如何创建 Azure 支持请求 | Microsoft Docs
description: 需要帮助的客户可以使用 Azure 门户查找自助服务解决方案以及创建和管理支持请求。
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 01/23/2020
ms.author: kfollis
ms.openlocfilehash: 28a29bbb94f63657a69e873bb8f969e96ee92c76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248484"
---
# <a name="how-to-create-an-azure-support-request"></a>如何创建 Azure 支持请求

## <a name="overview"></a>概述

Azure 使您能够创建和管理支持请求（也称为支持票证）。 您可以在[Azure 门户](https://portal.azure.com)中创建和管理请求，本文将介绍这一点。 您还可以使用[Azure 支持票证 REST API](/rest/api/support)以编程方式创建和管理请求。

> [!NOTE]
> Azure 门户 URL 特定于部署组织的 Azure 云。
>
>* 用于商业用途的 Azure 门户是：[https://portal.azure.com](https://portal.azure.com)
>* 德国的 Azure 门户是：[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* 美国政府的 Azure 门户为：[https://portal.azure.us](https://portal.azure.us)
>
>

根据客户反馈，我们更新了支持请求体验，专注于三个主要目标：

* **简化 ：** 使支持和故障排除易于查找，并简化提交支持请求的方式。
* **集成**：在未切换上下文的情况下，在排除 Azure 资源的问题时，可以轻松地打开支持请求。
* **高效**：收集支持代理有效解决问题所需的关键信息。

## <a name="getting-started"></a>入门

您可以在 Azure 门户中获取**帮助和支持**。 它在 Azure 门户菜单、全局标头或服务的资源菜单中可用。 在提交支持请求之前，您必须具有适当的权限。

### <a name="role-based-access-control"></a>基于角色的访问控制

要创建支持请求，您必须是管理员，或者被分配到订阅级别的["支持请求参与者"](../../role-based-access-control/built-in-roles.md#support-request-contributor)角色。

### <a name="go-to-help--support-from-the-global-header"></a>转到"帮助" 从全局标头获得支持

要从 Azure 门户中的任意位置启动支持请求，请执行以下任务：

1. 选择 **？** 在全局标头中。 然后选择 **"帮助+支持**"。

   ![帮助和支持](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. 选择 **"新建支持请求**"。 按照提示向我们提供有关您的问题的信息。 我们将建议一些可能的解决方案，收集有关问题的详细信息，并帮助您提交和跟踪支持请求。

   ![新建支持请求](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>转到资源菜单中的帮助和支持

要在资源上下文中启动支持请求，您当前正在处理：

1. 在资源菜单中，在 **"支持 + 故障排除"** 部分中，选择 **"新建支持请求**"。

   ![在上下文中](./media/how-to-create-azure-support-request/incontext2lower.png)

2. 按照提示向我们提供有关您遇到的问题的信息。 从资源启动支持请求过程时，会为您预先选择某些选项。

## <a name="create-a-support-request"></a>创建支持请求

我们将引导您完成一些步骤，收集有关您的问题的信息，并帮助您解决问题。 每个步骤都介绍在以下各节中。

### <a name="basics"></a>基础

支持请求过程的第一步是收集关于问题和支持计划的基本信息。

在新**支持请求**的 **"基本"** 选项卡上，使用选择器开始告诉我们有关问题。 首先，您将为问题类型标识一些常规类别，并选择相关的订阅。 选择服务，例如运行**Windows 的虚拟机**。 选择资源，如虚拟机的名称。 用您自己的词语描述问题，然后**选择问题类型**以变得更加具体。

![“基本信息”边栏选项卡](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure 提供对订阅管理的无限制支持，包括计费、配额调整和帐户转移。 对于技术支持，需要支持计划。 [了解关于支持计划的详细信息](https://azure.microsoft.com/support/plans)。
>
>

### <a name="solutions"></a>解决方案

收集基本信息后，我们接下来将向您展示自行尝试的解决方案。 在某些情况下，我们甚至可以运行快速诊断。 解决方案由 Azure 工程师编写，将解决最常见的问题。

### <a name="details"></a>详细信息

接下来，我们收集有关问题的其他详细信息。 在此步骤中提供全面而详细的信息有助于我们将您的支持请求路由到正确的代理。

如果可能，请告诉我们问题何时开始，以及重现问题的任何步骤。 您可以上载文件，例如日志文件或诊断输出。

获得有关问题的所有信息后，选择如何获得支持。 在 **"详细信息****"的"支持方法**"部分中，选择影响的严重性。 提供您首选的联系方式、与您联系的好时机以及您的支持语言。

接下来，完成**联系信息**部分，以便了解如何与您联系。

### <a name="review--create"></a>查看 + 创建

在每个选项卡上填写所有必需的信息，然后选择 **"审阅 + 创建**"。 检查您将发送给支持部门的详细信息。 如果需要，返回任何选项卡进行更改。 满足支持请求完成后，请选择 **"创建**"。

支持代理将使用您指示的方法与您联系。 有关初始响应时间的信息，请参阅[支持范围和响应能力](https://azure.microsoft.com/support/plans/response/)。

## <a name="all-support-requests"></a>所有支持请求

您可以通过访问 **"帮助 + 支持** >  **所有支持"请求**来查看支持请求的详细信息和状态。

![所有支持请求](./media/how-to-create-azure-support-request/allrequestslower.png)

在此页上，您可以按**订阅**、**创建**日期 （UTC） 和**状态**筛选支持请求。 另外，还可以在此页面上对支持请求进行排序和搜索操作。

选择支持请求以查看详细信息，包括严重性以及支持代理响应所需的预期时间。

如果要更改请求的严重性，请选择 **"业务影响**"。 从要分配的分量列表中选择。

> [!NOTE]
> 最大严重性级别取决于支持计划。 [了解关于支持计划的详细信息](https://azure.microsoft.com/support/plans)。
>
>
要了解有关 Azure 中的自助支持选项的详细信息，请观看此视频：

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>后续步骤

* [向我们发送您的反馈和建议](https://feedback.azure.com/forums/266794-support-feedback)
* [在推特上与我们](https://twitter.com/azuresupport)互动
* 在[MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure)中从您的同行那里获得帮助
* 在[Azure 支持常见问题解答](https://azure.microsoft.com/support/faq)中了解更多信息
