---
title: 如何重新激活已禁用的 Azure 访问控制服务 (ACS) 命名空间
description: 了解如何查找和启用 Azure 访问控制服务 (ACS) 命名空间，并请求延期启用到 2019 年 2 月 4 日为止。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 298afda7549690a9ea0314bff63a714be50a33b9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019737"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>如何：重新激活已禁用的访问控制服务命名空间

在 2017 年 11 月我们已宣布，Azure Active Directory (Azure AD) 服务 Microsoft Azure 访问控制服务 (ACS) 将在 2018 年 11 月 7 日停用。

然后，我们在 2018 年 11 月 7 日停用日期之前的 12 个月、9 个月、6 个月、3 个月、1 个月、2 周、1 周和 1 天，向 ACS 订阅管理员发送了多封有关 ACS 停用的电子邮件。

在 2018 年 10 月 3 日我们（通过电子邮件和[博客文章](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)）宣布，2018 年 11 月 7 日之前未完成迁移的客户可以请求延期。 通告中还包含了有关请求延期的说明。

## <a name="why-your-namespace-is-disabled"></a>为何禁用你的命名空间

如果你尚未选择请求延期，我们将从 2018 年 11 月 7 日开始禁用你的 ACS 命名空间。 如果你错过了这些信件，但仍希望延期到 2019 年 2 月 4 日，请遵照以下部分中的说明操作。

> [!NOTE]
> 只有订阅的管理员才能运行 PowerShell 命令和请求延期。

## <a name="find-and-enable-your-acs-namespaces"></a>查找和启用 ACS 命名空间

可以使用 ACS PowerShell 列出所有 ACS 命名空间，并重新激活已禁用的命名空间。

1. 下载并安装 ACS PowerShell。
    1. 转到 PowerShell 库并下载 [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)。
    1. 安装该模块：

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. 获取所有可用命令的列表：

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        若要获取有关特定命令的帮助，请运行：

        ```
        Get-Help [Command-Name] -Full
        ```
    
        其中，`[Command-Name]` 是 ACS 命令的名称。
1. 使用 **Get-AcsSubscription** cmdlet 列出可用的 Azure 订阅。
1. 使用 **Get-AcsNamespace** cmdlet 列出 ACS 命名空间。
1. 通过检查 `State` 是否为 `Disabled`，来确认命名空间是否已禁用。

    [![确认命名空间是否已禁用](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    还可以使用 `nslookup {your-namespace}.accesscontrol.windows.net` 来确认域是否仍处于活动状态。

1. 使用 **Enable-AcsNamespace** cmdlet 启用 ACS 命名空间。

    启用命名空间后，可以请求延期，使命名空间不会在 2019 年 2 月 4 日之前再次禁用。 该日期过后，对 ACS 的所有请求都将失败。

## <a name="request-an-extension"></a>请求延期

1. 通过 `https://{your-namespace}.accesscontrol.windows.net` 导航到 ACS 命名空间的管理门户。
1. 选择“阅读条款”按钮以阅读[更新的使用条款](https://azure.microsoft.com/support/legal/access-control/)；此时会定向到包含已更新的使用条款的页面。

    [![选择“阅读条款”按钮](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. 在页面顶部的横幅中选择“请求延期”。 只有在阅读[更新的使用条款](https://azure.microsoft.com/support/legal/access-control/)之后，才会启用此按钮。

    [![选择“请求延期”按钮](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. 注册延期请求后，页面将会刷新，其顶部会出现一个新横幅。

    [![刷新后包含更新的横幅的页面](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)

## <a name="help-and-support"></a>帮助和支持

- 如果在遵循此操作说明之后遇到任何问题，请联系 [Azure 支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。
- 如果在 ACS 停用方面有任何疑问或反馈，请联系我们：acsfeedback@microsoft.com。

## <a name="next-steps"></a>后续步骤

- 在[如何：从 Azure 访问控制服务迁移](active-directory-acs-migration.md)中查看有关 ACS 停用的信息。
