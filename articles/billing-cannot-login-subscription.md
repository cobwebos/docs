---
title: "无法登录到 Azure 订阅 | Microsoft Docs"
description: "说明如何解决某些常见的 Azure 订阅登录问题。"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/2/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 030b41eaa405ff70081a4c15cabded08a1b0b07a


---
# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>无法登录以管理 Azure 订阅
本文介绍了一些解决登录问题的最常见方法。

## <a name="page-hangs-in-the-loading-status"></a>页面在加载状态下挂起
如果 Internet 浏览器页面挂起，请尝试进行以下每个步骤，直到能够转到 [Azure 门户](https://portal.azure.com)为止。

* 刷新页面。
* 使用不同的 Internet 浏览器。
* 如果使用的是 Microsoft Internet Explorer，请使用 InPrivate 浏览模式浏览到 Azure 门户。 
  
  A.    单击“工具”![工具按钮](./media/billing-cannot-login-subscription/Toolsbutton.png) > **“安全”** > **“InPrivate 浏览”**。
  
  B.    浏览到 [Azure 门户](https://portal.azure.com)，然后登录到该门户。

## <a name="error-message-no-subscriptions-found"></a>错误消息“找不到任何订阅”
如果帐户没有足够的权限，则可能会看到“找不到任何订阅”的错误消息。 请确保以正确的管理员身份登录。 帐户管理员只能访问[帐户中心](https://account.windowsazure.com/Subscriptions)。 服务管理员 (SA) 和共同管理员 (CA) 则只能访问 [Azure 门户](https://portal.azure.com)或 Azure 经典门户。

**场景 1：[Azure 门户](https://portal.azure.com)收到错误消息**

若要解决此问题，请为帐户[添加共同管理员或所有者角色](billing-add-change-azure-subscription-administrator.md)。

**场景 2：[Azure 帐户中心](https://account.windowsazure.com/Subscriptions)收到错误消息**

请检查使用的帐户是否是帐户管理员。 若要验证谁是帐户管理员，请执行下列步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“订阅”。
3. 选择要检查的订阅，然后选择“设置”。
4. 选择“属性”。 订阅的帐户管理员会显示在“帐户管理员”框中。

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>用户以其他用户身份自动登录
如果在 Internet 浏览器中使用多个用户帐户，则可能出现此问题。

若要解决此问题，请尝试下列方法：

* 清除缓存并删除 Internet Cookie。 在 Internet Explorer 中，单击“工具” ![工具按钮](./media/billing-cannot-login-subscription/Toolsbutton.png) > “Internet 选项” > “删除”。 确保选中临时文件、cookie、密码和浏览历史记录的复选框，然后单击“删除”。
* 重置 Internet Explorer 设置，还原所做的任何个人设置。 请单击“工具”![工具按钮](./media/billing-cannot-login-subscription/Toolsbutton.png)> “Internet 选项” > “高级”> 选中“删除个人设置”框 >“重置”。
* 在“InPrivate 浏览”模式下浏览到 Azure 门户。 单击“工具”![工具按钮](./media/billing-cannot-login-subscription/Toolsbutton.png) > **“安全”** > **“InPrivate 浏览”**。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)以快速解决问题。 




<!--HONumber=Dec16_HO2-->


