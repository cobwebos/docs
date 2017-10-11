---
title: "Azure AD v2 Windows 桌面获取已启动 – 配置 |Microsoft 文档"
description: "如何获取访问令牌和调用受 Azure Active Directory v2 终结点的 API 的 Windows 桌面.NET (XAML) 应用程序。 |Microsoft Azure |Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 1dfaa7ade664e43dcb9aa788b0197ca17e6ec4cc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
## <a name="create-an-application-express"></a>创建应用程序 （速成版）
现在你需要注册你的应用程序中*Microsoft 应用程序注册门户*:
1. 注册你的应用程序通过[Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)
2.  输入你的应用程序和你的电子邮件的名称
3.  请确保选中的引导式安装的选项
4.  按照说明获取应用程序 ID 并将其粘贴到你的代码

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>将应用程序注册信息添加到你的解决方案 （高级）
现在你需要注册你的应用程序中*Microsoft 应用程序注册门户*:
1. 转到[Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序
2. 输入你的应用程序和你的电子邮件的名称 
3. 请确保未选中的引导式安装的选项
4. 单击`Add Platform`，然后选择`Native Application`和命中保存
5. 复制应用程序 id 的 GUID，请返回到 Visual Studio 中，打开`App.xaml.cs`和替换`your_client_id_here`刚刚注册的应用程序 id:

```csharp
private static string ClientId = "your_application_id_here";
```
