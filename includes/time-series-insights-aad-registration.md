---
title: include 文件
description: include 文件
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 08/08/2019
ms.openlocfilehash: ed5cd9424630a90b989b8a4d76959961f82a53ec
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935261"
---
> [!IMPORTANT]
> * 新的“Azure Active Directory”   > “应用注册”  边栏选项卡取代了旧的“Azure Active Directory”   > “应用注册(旧版)”  边栏选项卡 2019 年 5 月版。
> * 在旧版边栏选项卡中创建或显示的应用注册将自动显示在新的边栏选项卡中。
> * 有关迁移到新的 Azure 应用注册体验的全面信息，请阅读 [Azure 应用注册培训指南](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide)和 [Azure Active Directory 快速入门](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)。

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，依次选择“Azure Active Directory”   > “应用注册”   > “新注册”  。

   [![Azure Active Directory 中的新应用程序注册](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > 使用新的 Azure Active Directory 应用注册面板，可通过选择“拥有的应用程序”筛选显示的应用  。

    你的应用在注册后将在此处列出。

1. 为应用程序提供名称，然后选择“仅此组织目录中的帐户”  ，以指定可访问 API 的“支持的帐户类型”  。 选择要在用户进行身份验证后将其重定向到的有效 URI，然后选择“注册”  。

   [![在 Azure Active Directory 中创建应用程序](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. 重要的 Azure Active Directory 应用信息显示在所列应用的“概述”  边栏选项卡中。 在“拥有的应用程序”下，选择你的应用  ，然后选择“概述”  。

   [![复制应用程序 ID](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   复制要在客户端应用程序中使用的“应用程序(客户端) ID”  。

1. “身份验证”  边栏选项卡可指定重要的身份验证配置设置。 

    1. “重定向 URI”  必须与身份验证请求所提供的地址相匹配：

        * 对于本地开发环境中托管的应用，请选择“公共客户端(移动和桌面)”  。 确保将“默认客户端类型”设置为“是”  。
        * 对于 Azure 应用服务上托管的单页应用，请选择“Web”  。

    1. 检查“ID 令牌”以启用隐式授权流  。

   [![创建新客户端机密](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   单击“ **保存**”。

1. 选择“证书和机密”   ，然后选择“新建客户端机密”来创建客户端可用来证明其身份的应用程序密码。

   [![创建新客户端机密](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   然后，客户端密钥密码将会显示。 将密钥复制到喜爱的文本编辑器中。

   > [!NOTE]
   > 可以改为导入证书。 为增强安全性，建议使用证书。 若要使用证书，请选择  “上传证书”。

1. 将 Azure Active Directory 应用与 Azure 时序见解关联。 依次选择“API 权限”   > “添加权限”   > “我的组织使用的 API”  。 

    [![将 API 与 Azure Active Directory 应用相关联](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   在搜索栏中键入 `Azure Time Series Insights`，然后选择 `Azure Time Series Insights`。

1. 接下来，指定你的应用所需的 API 权限类型。 默认情况下，将突出显示“委派的权限”  。 然后，选择一种权限类型，再选择“添加权限”  。

    [![指定你的应用所需的 API 权限类型](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)