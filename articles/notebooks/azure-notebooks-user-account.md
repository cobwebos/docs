---
title: 登录到 Azure Notebooks
description: 使用 Microsoft 帐户或工作/学校帐户配置 Azure Notebooks 的用户帐户。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0d657fcc-26bc-41dd-abf0-3e5cfd66e0e0
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: kraigb
ms.openlocfilehash: 311f4d612c9deab23e7537b0c53ff3932c312cd4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60632006"
---
# <a name="your-user-account-for-azure-notebooks"></a>Azure Notebooks 的用户帐户

无论是否使用用户帐户登录，都可以使用 Azure Notebooks：

- 无需登录，你可以创建和运行 Notebooks，但不能将 Notebooks 或数据文件保留为项目的一部分。 例如，收到 Azure Notebooks 链接的用户无需登录即可享受笔记本。
- 登录后，Azure Notebooks 将保留你帐户中的所有项目。 登录用户也有一个用户 ID，使其可与他人共享项目和 Notebooks。
  - 当用于 Azure Notebooks 的帐户也与 Azure 订阅相关联时，你将获得其他好处，例如在功能更强大的服务器上运行 Notebooks、创建私有 Notebooks 以及向单个用户授予 Notebooks 权限。

登录 Azure Notebooks 需要 Microsoft 帐户或“工作或学校”帐户。 在 Notebooks 页右上侧选择“登录”命令时，将提示你输入帐户  ：

![Azure Notebooks 的登录命令](media/accounts/sign-in-command.png)

Azure Notebooks 中执行的所有工作都与你登录的帐户相关联。 每个帐户还必须在[用户配置文件](azure-notebooks-user-profile.md)中具有唯一的用户 ID。 因此，如果需要维护单独的项目集和单独的标识，则可以使用不同的帐户登录 Azure Notebooks。 例如，数据科学团队的每个成员可能同时拥有两个个人帐户（在共享的组帐户两边），用于向公司外部的人员展示他们的工作。 类似地，教师可能会为他们的教学角色维护一个不同于外部咨询或开源工作中使用的帐户。

## <a name="microsoft-accounts"></a>Microsoft 帐户

Microsoft 帐户用于登录任何数量的 Microsoft 产品和服务，如 Windows、Azure、Outlook.com、OneDrive 和 Xbox Live。 如果使用这些服务中的任何一种，则可能已经拥有可以与 Azure Notebooks 一起使用的 Microsoft 帐户。

如果不确定，请在帐户提示中选择“创建一个”命令  。 可使用任何提供程序的任何电子邮件地址创建新的 Microsoft 帐户。

![用于创建新 Microsoft 帐户的命令](media/accounts/create-new-microsoft-account.png)

> [!Note]
> 如果尝试使用已与某个帐户关联的电子邮件地址创建新帐户，可能会看到消息“你无法使用工作或学校电子邮件地址在此处注册。 请使用个人电子邮件地址(如 Gmail 或 Yahoo! 电子邮件地址)，或创建新的 Outlook 电子邮件地址。” 在这种情况下，请尝试使用工作电子邮件地址登录而不是创建一个新的帐户。

对于子帐户，默认情况下会阻止对 Azure Notebooks 的访问。 使用子帐户登录会显示以下错误：

![尝试使用子帐户登录时出错：出现问题，父级已阻止访问权限](media/accounts/child-account-error.png)

要启用访问权限，父级必须执行以下步骤：

1. 访问 `https://account.live.com/mk` 并使用父帐户登录。
1. 在相关子部分中，选择“管理子级对第三方应用的访问权限”  。
1. 在下一页，选择“启用访问权限”  。
1. 接下来使用子帐户登录 Azure Notebooks 时，在出现的权限提示符中选择“是”  。

> [!Warning]
> 启用对 Azure Notebooks 的第三方应用的访问权限后，还可访问所有其他第三方应用。 建议父级在启用访问权限时谨慎，且更密切地监视其子活动。

## <a name="work-or-school-accounts"></a>工作或学校帐户

工作或学校帐户由组织管理员创建，以使组织成员能够访问 Microsoft 云服务（如 Office 365），还可作为帐户在已加入域的计算机上登录 Windows。 工作或学校帐户通常使用组织电子邮件地址，例如 any-user@contoso.com。

使用工作或学校帐户登录 Azure Notebooks 可能需要管理员同意，因为 Azure Notebooks 收集或使用（但不披露）信息，如帐户的电子邮件地址和用户的浏览器信息。 （浏览器数据用于根据常见用途优化功能。）

如果用户被限制单独同意，则组织帐户的管理员必须代表用户提供同意。 在这种情况下，用户会看到消息“你无法访问此应用程序”：

![使用工作或学校帐户时出现的“你无法访问此应用程序”消息](media/accounts/consent-permissions-denied.png)

要以管理员身份提供同意，请使用[管理员同意页面](https://notebooks.azure.com/account/adminConsent)，该页面将引导你完成整个过程。

## <a name="next-steps"></a>后续步骤  

> [!div class="nextstepaction"]
> [编辑配置文件和用户 ID](azure-notebooks-user-profile.md)
