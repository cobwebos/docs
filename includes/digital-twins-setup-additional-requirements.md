---
author: baanders
description: 在 Azure 数字孪生安装程序中包含可能的其他要求的文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b490b4304dd74d8266b24d0ea5af58726b14d747
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125764"
---
你的组织可能需要订阅所有者的其他操作才能成功设置应用注册（进而完成设置可用的 Azure 数字孪生实例）。 根据组织的特定设置，所需的步骤可能会有所不同。

下面是所有者可能需要执行的一些常见潜在活动。 这些操作和其他操作可以从 Azure 门户的 " [*Azure AD 应用注册*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)" 页中执行。
* 向管理员授予对应用注册的许可。 你的组织可能需要对订阅中的所有应用注册 Azure AD 全局启用 "*管理员许可*"。 如果是这样，所有者需要在应用注册的 " *API 权限*" 页上为你的公司选择此按钮，以使应用注册有效：

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/grant-admin-consent.png" alt-text="API 权限下的 "授予管理员许可" 按钮的门户视图":::
  - 如果已成功授予同意，Azure 数字孪生的条目应显示 "同意" _ **（你的公司）** _的*状态*值
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/granted-admin-consent.png" alt-text="在 API 权限下为公司授予的管理员许可的门户视图":::
  - 所有者可能更倾向于对创建的每个应用注册重复此过程，或对此进行一次注册，并为订阅中的所有 Azure 数字孪生实例建立单个共享应用注册。 在第二种情况下，所有者应与需要使用应用注册的开发人员共享应用注册的*客户端 id*和*租户 id* 。 （这是在 Microsoft 自己的租户中完成此操作的方式）。
* 激活公共客户端访问
* 为 web 和桌面访问设置特定回复 Url
* 允许隐式 OAuth2 身份验证流

有关应用注册及其不同选项的详细信息，请参阅[*将应用程序注册到 Microsoft 标识平台*](https://docs.microsoft.com/graph/auth-register-app-v2)。

现在，你已有一个 Azure 数字孪生实例准备就绪，已分配了管理它的权限，并为客户端应用程序设置了权限来访问它。