---
title: include 文件
description: include 文件
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 09/24/2019
ms.openlocfilehash: 1ed57e30273c9ba3feb110e0e5acbd3bcc7ffd40
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74062853"
---
1. 在 [Azure 门户](https://ms.portal.azure.com/)中，依次选择“Azure Active Directory” > “应用注册” > “新注册”。

   [![Azure Active Directory 中的新应用程序注册](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    你的应用在注册后将在此处列出。

1. 为应用程序提供名称，然后选择“仅此组织目录中的帐户”，以指定可访问 API 的“支持的帐户类型”。 选择要在用户进行身份验证后将其重定向到的有效 URI，然后选择“注册”。

   [![在 Azure Active Directory 中创建应用程序](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. 重要的 Azure Active Directory 应用信息显示在所列应用的“概述”边栏选项卡中。 在“拥有的应用程序”下，选择你的应用，然后选择“概述”。

   [![复制应用程序 ID](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   复制要在客户端应用程序中使用的“应用程序(客户端) ID”。

1. “身份验证”边栏选项卡可指定重要的身份验证配置设置。 

    1. “重定向 URI”必须与身份验证请求所提供的地址相匹配：

        * 对于本地开发环境中托管的应用，请选择“公共客户端(移动和桌面)”。 确保将“默认客户端类型”设置为“是”。
        * 对于 Azure 应用服务上托管的单页应用，请选择“Web”。

    1. 检查“ID 令牌”以启用隐式授权流。

   [![创建新客户端机密](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   单击“保存”。

1. 选择“证书和机密”，然后选择“新建客户端机密”来创建客户端可用来证明其身份的应用程序密码。

   [![创建新客户端机密](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   然后，客户端密钥密码将会显示。 将密钥复制到喜爱的文本编辑器中。

   > [!NOTE]
   > 可以改为导入证书。 为增强安全性，建议使用证书。 若要使用证书，请选择“上传证书”。

1. 将 Azure Active Directory 应用与 Azure 时序见解关联。 依次选择“API 权限” > “添加权限” > “我的组织使用的 API”。 

    [![将 API 与 Azure Active Directory 应用相关联](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   在搜索栏中键入 `Azure Time Series Insights`，然后选择 `Azure Time Series Insights`。

1. 接下来，指定你的应用所需的 API 权限类型。 默认情况下，将突出显示“委派的权限”。 然后，选择一种权限类型，再选择“添加权限”。

    [![指定你的应用所需的 API 权限类型](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)