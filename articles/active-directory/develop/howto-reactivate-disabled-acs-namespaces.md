---
title: 如何重新激活已禁用的 Azure 访问控制服务 (ACS) 命名空间
description: 了解如何查找和启用 Azure 访问控制服务 (ACS) 命名空间，并请求延期启用到 2019 年 2 月 4 日为止。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cb17109d53c6e08c8607959207e367086032f6d
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482711"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>如何：重新激活禁用的访问控制服务命名空间

在 2017 年 11 月我们已宣布，Azure Active Directory (Azure AD) 服务 Microsoft Azure 访问控制服务 (ACS) 将在 2018 年 11 月 7 日停用。

然后，我们在 2018 年 11 月 7 日停用日期之前的 12 个月、9 个月、6 个月、3 个月、1 个月、2 周、1 周和 1 天，向 ACS 订阅管理员发送了有关 ACS 停用的电子邮件。

在 2018 年 10 月 3 日我们（通过电子邮件和[博客文章](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)）宣布，2018 年 11 月 7 日之前未完成迁移的客户可以请求延期。 公告还包含了有关请求延期的说明。

## <a name="why-your-namespace-is-disabled"></a>为何禁用你的命名空间

如果你尚未选择请求延期，我们将从 2018 年 11 月 7 日开始禁用 ACS 命名空间。 必须已请求延期到 2019 年 2 月 4 日；否则，将无法通过 PowerShell 启用命名空间。

> [!NOTE]
> 只有订阅的服务管理员或共同管理员才能运行 PowerShell 命令和请求延期。

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

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        其中，`[Command-Name]` 是 ACS 命令的名称。
1. 使用 **Connect-AcsAccount** cmdlet 连接到 ACS。 

    可能需要先通过运行 **Set-ExecutionPolicy** 来更改执行策略，然后才能运行该命令。
1. 使用 **Get-AcsSubscription** cmdlet 列出可用的 Azure 订阅。
1. 使用 **Get-AcsNamespace** cmdlet 列出 ACS 命名空间。
1. 通过检查 `State` 是否为 `Disabled`，来确认命名空间是否已禁用。

    [![确认命名空间是否已禁用](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    还可以使用 `nslookup {your-namespace}.accesscontrol.windows.net` 来确认域是否仍处于活动状态。

1. 使用 **Enable-AcsNamespace** cmdlet 启用 ACS 命名空间。

    启用命名空间后，可以请求延期，使命名空间不会在 2019 年 2 月 4 日之前再次禁用。 该日期过后，对 ACS 的所有请求都将失败。

## <a name="request-an-extension"></a>请求延期

自 2019 年 1 月 21 日开始，我们将接受新的延期请求。

我们将针对已请求延期到 2019 年 2 月 4 日的客户，禁用命名空间。 你仍然可以通过 PowerShell 重新启用命名空间，但是 48 小时后命名空间将再次禁用。

2019 年 3 月 4 日以后，客户将无法通过 PowerShell 重新启用任何命名空间。

此后将不再自动批准进一步的延期。 如果需要额外的迁移时间，请联系提供详细的迁移时间表的 [Azure 支持](https://portal.azure.com/#create/Microsoft.Support)。

### <a name="to-request-an-extension"></a>若要请求延期

1. 登录到 Azure 门户并创建[新的支持请求](https://portal.azure.com/#create/Microsoft.Support)。
1. 如下面的示例所示，填写新的支持请求表单。

    | 支持请求字段 | 值 |
    |-----------------------|--------------------|
    | **问题类型** | `Technical` |
    | **订阅** | 设置为你的订阅 |
    | **服务** | `All services` |
    | **资源** | `General question/Resource not available` |
    | **问题类型** | `ACS to SAS Migration` |
    | **主题** | 描述问题 |

   ![显示新的技术支持请求的示例](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>帮助和支持

- 如果在遵循此操作说明之后遇到任何问题，请联系 [Azure 支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。
- 如果在 ACS 停用方面有任何疑问或反馈，请联系我们：acsfeedback@microsoft.com。

## <a name="next-steps"></a>后续步骤

- 在[如何：从 Azure 访问控制服务迁移](active-directory-acs-migration.md)中，查看有关 ACS 停用的信息。
