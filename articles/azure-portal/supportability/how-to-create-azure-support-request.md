---
title: 如何创建 Azure 支持请求 | Microsoft Docs
description: 需要协助的客户可在 Azure 门户中查找自助服务解决方案并创建和管理支持请求。
services: Azure Supportability
author: mgblythe
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: how-to
ms.date: 06/25/2020
ms.author: mblythe
ms.openlocfilehash: d8a480481e47995f9f819122a3e8cc0b0a21a254
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851894"
---
# <a name="create-an-azure-support-request"></a>创建 Azure 支持请求

通过 Azure，可创建和管理支持请求（也称为支持票证）。 可在 [Azure 门户](https://portal.azure.com)中创建和管理请求，本文将讲到这一点。 还可使用 [Azure 支持票证 REST API](/rest/api/support) 以编程方式创建和管理请求。

> [!NOTE]
> Azure 门户 URL 特定于 Azure 云，你的组织在云端部署。
>
>* 商业用途的 Azure 门户是：[https://portal.azure.com](https://portal.azure.com)
>* 德国的 Azure 门户是：[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* 美国政府的 Azure 门户是：[https://portal.azure.us](https://portal.azure.us)

支持请求体验侧重于三个主要目标：

* **简化**：让用户更轻松就能找到支持和疑难解答，同时简化了提交支持请求的方式。
* **集成**：可在排查 Azure 资源问题时，不切换上下文就轻松打开支持请求。
* **高效**：收集支持工程师高效解决问题所需的关键信息。

Azure 对订阅管理（包括计费、配额调整和帐户转移）提供无限支持。 对于技术支持，需要支持计划。 有关详细信息，请参阅[比较支持计划](https://azure.microsoft.com/support/plans)。

## <a name="getting-started"></a>开始使用

可转到 Azure 门户中的“帮助 + 支持”。 可通过 Azure 门户菜单、全局标头或服务的资源菜单访问该功能。 你必须先具有适当的权限，然后才可提出支持请求。

### <a name="role-based-access-control"></a>基于角色的访问控制

若要创建支持请求，你必须是[所有者](../../role-based-access-control/built-in-roles.md#owner)、[参与者](../../role-based-access-control/built-in-roles.md#contributor)，或在订阅级别被分配到[支持请求参与者](../../role-based-access-control/built-in-roles.md#support-request-contributor)角色。 若要创建不包含订阅的支持请求，例如 Azure Active Directory 方案，则必须是[管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

### <a name="go-to-help--support-from-the-global-header"></a>通过全局标头转到“帮助 + 支持”

若要在 Azure 门户的任意位置启动支持请求：

1. 选择全局标头中的“?” 。 然后选择“帮助 + 支持”。

   ![帮助 + 支持](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

1. 选择“新建支持请求”。 按照提示来提供有关问题的信息。 我们将建议一些可能的解决方案，收集问题的相关详细信息，并帮助你提交和跟踪支持请求。

   ![新建支持请求](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>通过资源菜单转到“帮助 + 支持”

若要在你当前正在处理的资源的上下文中启动支持请求：

1. 从资源菜单的“支持 + 疑难解答”部分，选择“新建支持请求” 。

   ![在上下文中](./media/how-to-create-azure-support-request/incontext2lower.png)

1. 按照提示提供你当前遇到的问题的相关信息。 从资源开始支持请求过程时，一些选项已经过预选择。

## <a name="create-a-support-request"></a>创建支持请求

我们将引导你完成一些步骤，以收集你所遇问题的相关信息并帮助你解决该问题。 以下部分将介绍各个步骤。

### <a name="basics"></a>基本信息

支持请求过程的第一步是收集关于问题和支持计划的基本信息。

在“新建支持请求”的“基本信息”选项卡中，使用选择器开始提供问题相关信息 。 首先，你将确定问题类型的一些常规分类，并选择相关的订阅。 选择该服务，例如**运行 Windows 的虚拟机**。 选择资源，例如虚拟机的名称。 请用自己的字词描述问题，然后选择 "**问题类型**" 和 "**问题子类型**" 以获取更具体的信息。

![“基本信息”边栏选项卡](./media/how-to-create-azure-support-request/basics2lower.png)

### <a name="solutions"></a>解决方案

收集到基本信息后，我们接下来将提供你可自行尝试的解决方案。 在某些情况下，我们甚至可运行一次快速诊断。 这些解决方案由 Azure 工程师编写，可解决最常见的问题。

### <a name="details"></a>详细信息

接下来，我们会收集有关问题的其他详细信息。 在此步骤中提供详尽的详细信息可帮助我们将你的支持请求路由到正确的工程师。

1. 可能的话，请提供问题开始时间及任何重现步骤。 可上传文件，例如日志文件或诊断输出内容。 有关文件上传的详细信息，请参阅[文件上传准则](how-to-manage-azure-support-request.md#file-upload-guidelines)。

1. 在我们获得关于该问题的全部信息后，请选择如何获取支持。 在“详细信息”的“支持方法”部分，选择影响程度 。 最大严重性级别取决于你的[支持计划](https://azure.microsoft.com/support/plans)。

    默认情况下，选择 "**共享诊断信息**" 选项。 这允许 Azure 支持人员收集 Azure 资源中的[诊断信息](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)。 在某些情况下，默认情况下不会选择另一个问题，例如请求访问虚拟机的内存。

1. 提供首选联系方法、适宜联系时间和支持语言。

1. 接下来，完成“联系人信息”部分，让我们知道如何与你联系。

### <a name="review--create"></a>审阅 + 创建

完成每个选项卡上的所有必填信息，然后选择“审阅 + 创建”。 检查将发送以支持的详细信息。 必要时，请返回任何选项卡进行更改。 支持请求的完成情况让你满意时，请选择“创建”。

支持工程师将使用您指定的方法与您联系。 有关初始响应时间的信息，请参阅[支持范围和响应能力](https://azure.microsoft.com/support/plans/response/)。


## <a name="next-steps"></a>后续步骤

要详细了解 Azure 中的自助支持选项，请观看此视频：

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

请单击下面的链接了解详细信息：

* [如何管理 Azure 支持请求](how-to-manage-azure-support-request.md)
* [Azure 支持票证 REST API](/rest/api/support)
* [向我们发送反馈和建议](https://feedback.azure.com/forums/266794-support-feedback)
* 在 [Twitter](https://twitter.com/azuresupport) 上与我们联系
* 在 [Microsoft 问答页](https://docs.microsoft.com/answers/products/azure)上获取同行的帮助
* 在 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq)中了解详细信息
