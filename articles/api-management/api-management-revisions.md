---
title: Azure API 管理中的修订 |Microsoft Docs
description: 了解 Azure API 管理中的修订概念。
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 9a20a7966daff372cf5c0abc9b7b1dbbfd459838
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403232"
---
# <a name="revisions-in-azure-api-management"></a>Azure API 管理中的修订版本

使用修订，可以通过受控且安全的方式对 Api 进行更改。 若要进行更改，请创建新的修订版本。 然后，可以编辑和测试 API，而不会干扰 API 使用者。 准备就绪后，你将获得最新版本。 同时，你可以根据需要将条目发布到更改日志，以使 API 使用者保持最新状态。 更改日志将发布到开发人员门户。

> [!NOTE]
> 在“消耗”层中，开发人员门户不可用。

对于修订，你可以：

- 安全地对你的 API 定义和策略进行更改，而不会干扰你的生产 API。
- 在发布更改之前尝试更改。
- 记录所做的更改，使开发人员可以了解新内容。
- 如果发现问题，请回滚。

[遵循我们的演练开始使用修订。](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>访问特定的修订版本

可以使用特殊格式的 URL 访问 API 的每个修订版本。 `;rev={revisionNumber}`在 API URL 的末尾，但在查询字符串之前追加，以访问该 api 的特定修订版本。 例如，你可以使用此 URL 访问 API 的修订版 3 `customers` ：

`https://apis.contoso.com/customers;rev=3?customerId=123`

默认情况下，每个修订版本与当前修订版本具有相同的安全设置。 如果希望为每个修订应用不同的安全性，则可以特意更改特定版本的策略。 例如，你可能想要添加 [IP 筛选策略](./api-management-access-restriction-policies.md#RestrictCallerIPs) 以阻止外部调用方访问仍在开发的修订版本。

修订可以脱机，这使得调用方可以访问它，即使它们尝试通过其 URL 访问修订版本也是如此。 您可以使用 Azure 门户将修订标记为脱机。 如果使用 PowerShell，则可以使用 `Set-AzApiManagementApiRevision` cmdlet 并将 `Path` 参数设置为 `$null` 。

> [!NOTE]
> 建议在不使用修订时将其脱机。

## <a name="current-revision"></a>当前修订

单个修订版本可设置为 *当前* 修订版本。 此修订版本将用于未在 URL 中指定显式修订号的所有 API 请求。 您可以通过将该修订设置为 "当前" 来回滚到以前的修订版本。

您可以使用 Azure 门户将修订设置为当前版本。 如果使用 PowerShell，则可以使用 `New-AzApiManagementApiRelease` cmdlet。

## <a name="revision-descriptions"></a>修订说明

创建修订版本时，可以为自己的跟踪目的设置描述。 未向 API 用户播放说明。

将修订版设置为 "当前" 时，还可以选择指定公共更改日志说明。 更改日志包含在 API 用户要查看的开发人员门户中。 你可以使用 PowerShell cmdlet 来修改更改日志说明 `Update-AzApiManagementApiRelease` 。

## <a name="versions-and-revisions"></a>版本和修订版本

版本和修订版本都是不同的。 与非版本化的 API 一样，每个版本都可以有多个修订版本。 您可以使用修订版，而无需使用版本或其他方法。 通常，版本用于将 API 版本与重大更改分隔开来，而修订可用于对 API 进行少量和非重大更改。

如果你发现你的修订版本发生了重大更改，或者如果你想要将修订版正式转换为 beta/test 版本，则可以从修订版本创建版本。 使用 Azure 门户，单击 "修订" 选项卡上的修订上下文菜单中的 "从修订版创建版本"。
