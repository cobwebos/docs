---
title: "教程：Azure Active Directory 与 Evidence.com 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Evidence.com 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f9a7cb7c-ff67-40dc-872c-1fa35f9dd03b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5d5183b136a0ceca939f754f67130d1179f6f4f7


---
# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>教程：Azure Active Directory 与 Evidence.com 的集成
本教程的目的是介绍如何在 Azure Active Directory (AAD) 和 Evidence.com 之间设置单一登录。 在本教程中概述的方案假定已具有以下各项：

* 有效的 Microsoft Azure 订阅
* 启用了单一登录的 Evidence.com 订阅（如果未启用基于 SAML 的单一登录，请向 earlyaccess@evidence.com 发送电子邮件）

完成本教程后，你为其分配了 Evidence.com 访问权限的 AAD 用户将能够使用 AAD 访问面板以单一登录方式登录到该应用程序。

## <a name="add-evidencecom-to-your-directory"></a>将 Evidence.com 添加到目录
本部分概述了如何将 Evidence.com 添加为 Azure Active Directory 中的集成应用程序。

**若要为 Evidence 启用应用程序集成，请执行以下步骤：**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，在左侧导航窗格上，单击“Active Directory”。
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
4. 若要打开应用程序库，请单击“添加”，然后单击“从库中添加应用程序”。
5. 在搜索框中，键入“Evidence.com”。
6. 在结果窗格中，选择“Evidence.com”，然后单击“完成”以添加该应用程序。

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure Active Directory 中的帐户向 Evidence.com 证明自己的身份。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中添加 Evidence.com 后，单击“配置单一登录”。 
2. 在下一个屏幕上，选择“Azure AD 单一登录”，然后单击“下一步”。
3. 在“配置应用 URL”屏幕中，输入用户将用来登录你的 Evidence.com 租户的 URL（示例：https://yourtenant.evidence.com），然后单击“下一步”。 
4. 单击“下载证书”链接，并将证书保存到本地驱动器。 此证书和元数据 URL（实体 ID、SSO 登录 URL 和注销 URL）将用来在 Evidence.com 站点上设置 SSO。 
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 Evidence.com 租户，然后导航到“管理”选项卡。
6. 单击“代理单一登录”
7. 选择“基于 SAML 的单一登录”
8. 将 Azure 经典门户中显示的“颁发者 URL”、“单一登录 URL”和“单一注销 URL”值复制到 Evidence.com 中的对应字段。
9. 使用某个文本编辑器（例如 Notepad.exe）打开在步骤 4 中下载的证书，将其内容复制并粘贴到“安全证书”框中。 
10. 在 Evidence.com 中保存配置。
11. 在 Azure 经典门户中，选中“确认你已按照上述方法配置了单一登录”。 选中此复选框将使当前证书能够针对此应用程序复选框开始工作。
12. 在“单一登录确认”页上，单击“完成”。  

## <a name="creating-an-evidencecom-test-user"></a>创建 Evidence.com 测试用户
要使 Azure AD 用户能够登录，必须在 Evidence.com 应用程序中为其预配访问权限。 本部分介绍了如何在 Evidence.com 中创建 Azure AD 用户帐户

**若要在 Evidence.com 中预配用户帐户，请执行以下步骤：**

1. 在 Web 浏览器窗口中，以管理员身份登录到你的 Evidence.com 公司站点。
2. 导航到“管理”选项卡。
3. 单击“添加用户”。
4. 单击“添加”按钮。
5. 添加的用户的“电子邮件地址”必须与你要向其授予访问权限的用户在 Azure AD 中的用户名相匹配。 如果在你的组织中，用户名和电子邮件地址不是同一个值，则可以使用 Azure 经典门户的“Evidence.com”>“属性”>“单一登录”部分将发送到 Evidence.com 的 nameidenitifer 更改为电子邮件地址。

## <a name="assigning-users-to-evidencecom"></a>将用户分配到 Evidence.com
要使已预配的 AAD 用户能够在其访问面板上看到 Evidence.com，必须在 Azure 经典门户中为其分配访问权限。

**若要将用户分配到 Evidence.com，请执行以下步骤：**

1. 在 Azure 经典门户中，在 Evidence.com 的快速启动页上，单击“将用户分配到 Evidence.com”。
2. 在“显示”菜单中，选择是要将用户还是将组分配到 Evidence.com，然后单击对号按钮。
3. 在“用户”列表中，选择要分配到 Evidence.com 的用户或组。
4. 在页脚中，单击“分配”按钮。




<!--HONumber=Nov16_HO3-->


