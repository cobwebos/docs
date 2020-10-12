---
title: 条件访问 API 和 PowerShell - Azure Active Directory
description: 使用 Azure AD 条件访问 API 和 PowerShell 像管理代码一样管理策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33f3766e02316eccb519bea15246541531750ed1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90007971"
---
# <a name="conditional-access-programmatic-access"></a>条件访问：以编程方式访问

许多组织已经表示需要尽可能地像管理代码一样管理他们的环境。 使用 Microsoft Graph，你可以像对待环境中的任何其他代码片段一样对待条件访问策略。

Microsoft Graph 提供了一个统一的可编程性模型，组织可使用该模型与 Microsoft 365、Windows 10 和企业移动性 + 安全性中的数据交互。 有关 Microsoft Graph 的详细信息，请参阅 [Microsoft Graph 概述](/graph/overview)一文。

![一个图像，显示了作为图形的一部分的主要资源和关系](./media/howto-conditional-access-apis/microsoft-graph.png)

以下示例按原样提供，不提供支持。 你可以将这些示例用作组织中工具的基础。 

下面的许多示例使用[托管标识](../managed-identities-azure-resources/overview.md)、[逻辑应用](../../logic-apps/logic-apps-overview.md)、[OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage)、[Teams](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/) 和 [Azure Key Vault](../../key-vault/general/overview.md) 之类的工具。

## <a name="configure"></a>配置

### <a name="powershell"></a>PowerShell

对于许多管理员而言，PowerShell 已经是一个很熟悉的脚本编写工具。 以下示例演示了如何使用 [Azure AD PowerShell 模块](https://www.powershellgallery.com/packages/AzureAD)来管理条件访问策略。

- [使用 Azure AD PowerShell 命令配置条件访问策略](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>图形 API

此示例显示了条件访问图形 API 中提供的基本的创建、读取、更新和删除 (CRUD) 选项。 此示例还包括了一些 JSON 模板，你可以用这些模板来创建一些示例策略。

- [使用 Microsoft Graph API 调用来配置条件访问策略](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>使用模板进行配置

使用条件访问 API，以便通过模板在预生产环境中部署条件访问策略。

- [使用 Microsoft Graph API 模板来配置条件访问策略](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>测试

此示例使用审批工作流为更安全的部署做法建模，这些工作流可以将条件访问策略从一个环境（例如预生产环境）复制到另一个环境（例如生产环境）。

- [从测试环境提升条件访问策略](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>部署

此示例提供了一种机制，用于逐步为用户群体执行分阶段的部署条件访问策略，使你能够及早管理支持影响并发现问题。

- [使用审批工作流将条件访问策略部署到生产环境](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>监视

此示例提供了一种机制，用于监视一段时间内的条件访问策略更改，可以在密钥策略被更改时触发警报。

- [监视已部署的针对更改的条件访问策略并触发警报](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>管理

### <a name="backup-and-restore"></a>备份和还原

使用此示例，通过 Teams 中的审批自动执行条件访问策略的备份和还原。

- [使用 Microsoft Graph API 调用来管理条件访问策略的备份和还原过程](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>紧急访问帐户

多个管理员可能会创建条件访问策略，并且可能会忘记将[紧急访问帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)添加为这些策略的排除项。 此示例可确保更新所有策略，以包括指定的紧急访问帐户。

- [使用 Microsoft Graph API 调用来管理向条件访问策略分配紧急访问帐户的操作](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>应变规划

事情并非始终如你所愿，当发生意外情况时，你需要一种方法来返回到可以继续工作的状态。 下面的示例提供了一种方法，用于将策略还原到已知的良好应变计划并禁用其他条件访问策略。

- [使用 Microsoft Graph API 调用来管理条件访问应变策略的激活](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>社区贡献

[GitHub 存储库](https://github.com/Azure-Samples/azure-ad-conditional-access-apis)中提供了这些示例。 我们很乐意通过 GitHub 问题和拉取请求来支持社区贡献事宜。

## <a name="next-steps"></a>后续步骤

- [Microsoft Graph 概述](/graph/overview)

- [条件访问 API](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-1.0)

- [命名位置 API](/graph/api/resources/namedlocation?view=graph-rest-1.0)
