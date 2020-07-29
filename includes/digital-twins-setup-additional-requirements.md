---
author: baanders
description: 在 Azure 数字孪生安装程序中包含可能的其他要求的文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 57be62d0f74f19e35eedf8720e2a6f5cbd3b45d4
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87335745"
---
你的组织可能需要订阅所有者的其他操作才能成功设置应用注册（进而完成设置可用的 Azure 数字孪生实例）。 根据组织的特定设置，所需的步骤可能会有所不同。

下面是所有者可能需要执行的一些常见潜在活动。 这些操作和其他操作可以从 Azure 门户的 " [*Azure AD 应用注册*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)" 页中执行。
* 向管理员授予对应用注册的许可。 你的组织可能需要对订阅中的所有应用注册 Azure AD 全局启用 "*管理员许可*"。 如果是这样，所有者需要在应用注册的 " *API 权限*" 页上为你的公司选择此按钮，以使应用注册有效：

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/grant-admin-consent.png" alt-text="API 权限下的 "授予管理员许可" 按钮的门户视图":::
  - 如果已成功授予同意，Azure 数字孪生的条目应显示 "同意" _ **（你的公司）** _的*状态*值
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/granted-admin-consent.png" alt-text="在 API 权限下为公司授予的管理员许可的门户视图":::
* 激活公共客户端访问
* 为 web 和桌面访问设置特定回复 Url
* 允许隐式 OAuth2 身份验证流

有关应用注册及其不同选项的详细信息，请参阅[*将应用程序注册到 Microsoft 标识平台*](https://docs.microsoft.com/graph/auth-register-app-v2)。

现在，你已有一个 Azure 数字孪生实例准备就绪，已分配了管理它的权限，并为客户端应用程序设置了权限来访问它。