---
title: 条件性访问 Api 和 PowerShell-Azure Active Directory
description: 使用 Azure AD 条件性访问 Api 和 PowerShell 来管理代码等策略
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
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007971"
---
# <a name="conditional-access-programmatic-access"></a>条件性访问：编程访问

很多组织都表示他们需要尽可能多地管理其环境（如代码）。 使用 Microsoft Graph 您可以将条件性访问策略视为您的环境中的任何其他代码段。

Microsoft Graph 提供了一个统一的可编程性模型，组织可使用该模型与 Microsoft 365、Windows 10 和企业移动性 + 安全性中的数据进行交互。 有关 Microsoft Graph 的详细信息，请参阅 [Microsoft Graph 概述一](/graph/overview)文。

![显示作为图形一部分的主要资源和关系的图像](./media/howto-conditional-access-apis/microsoft-graph.png)

以下示例按原样提供，不提供支持。 您可以使用这些示例作为组织中工具的基础。 

以下许多示例使用 [托管标识](../managed-identities-azure-resources/overview.md)、 [逻辑应用](../../logic-apps/logic-apps-overview.md)、 [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage)、 [团队](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/)和 [Azure Key Vault](../../key-vault/general/overview.md)等工具。

## <a name="configure"></a>配置

### <a name="powershell"></a>PowerShell

对于很多管理员而言，PowerShell 已经是一个理解的脚本编写工具。 下面的示例演示如何使用 [Azure AD PowerShell 模块](https://www.powershellgallery.com/packages/AzureAD) 来管理条件性访问策略。

- [使用 Azure AD PowerShell 命令配置条件访问策略](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>图形 API

此示例显示条件访问图形 Api 中可用的基本的创建、读取、更新和删除 (CRUD) 选项。 该示例还包含一些可用于创建示例策略的 JSON 模板。

- [使用 Microsoft Graph API 调用配置条件访问策略](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>使用模板配置

使用条件性访问 Api，使用模板在预生产环境中部署条件性访问策略。

- [使用 Microsoft Graph API 模板配置条件访问策略](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>测试

此示例使用批准工作流为安全的部署实践建模，该工作流可以将条件访问策略从一个环境（例如预生产）复制到另一个环境，如生产环境。

- [从测试环境升级条件性访问策略](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>部署

本示例提供了一种机制，用于向用户群体逐步执行分步部署条件性访问策略，使你能够及早管理支持影响和问题。

- [使用审批工作流将条件访问策略部署到生产环境](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>监视

此示例提供一种机制，用于监视一段时间内的条件性访问策略更改，并在更改密钥策略时触发警报。

- [监视已部署的条件访问策略，并触发警报](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>管理

### <a name="backup-and-restore"></a>备份和还原

使用此示例，自动执行条件性访问策略与团队审批的备份和还原。

- [使用 Microsoft Graph API 调用来管理条件访问策略的备份和还原过程](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>紧急访问帐户

多个管理员可能会创建条件性访问策略，并且可能会忘记添加 [紧急访问帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) 作为对这些策略的排除。 此示例确保所有策略都进行了更新，以包括指定的紧急访问帐户。

- [使用 Microsoft Graph API 调用管理向条件访问策略分配紧急访问帐户](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>应变规划

事情并非始终按您希望的方式工作，在这种情况下，您需要一种方法来返回到工作可以继续运行的状态。 下面的示例提供了一种方法，用于将策略还原到已知的良好应变计划并禁用其他条件性访问策略。

- [使用 Microsoft Graph API 调用来管理条件访问应变策略的激活](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>社区贡献

这些示例在我们的 [GitHub 存储库](https://github.com/Azure-Samples/azure-ad-conditional-access-apis)中提供。 我们非常乐意支持社区发布全面的 GitHub 问题和拉取请求。

## <a name="next-steps"></a>后续步骤

- [Microsoft Graph 概述](/graph/overview)

- [条件访问 API](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-1.0)

- [命名位置 API](/graph/api/resources/namedlocation?view=graph-rest-1.0)
