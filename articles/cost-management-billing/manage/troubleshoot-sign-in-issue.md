---
title: 排查 Azure 订阅登录问题
description: 帮助解决无法登录到 Azure 门户或 Azure 帐户中心的问题。
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: v-miegge
ms.openlocfilehash: 3ee600cb72d06781f87c8f68640576afa50cea06
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686490"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>排查 Azure 订阅登录问题

本指南可帮助解决无法登录到 Azure 门户或 Azure 帐户中心的问题。

> [!NOTE]
> 如果在注册新的 Azure 帐户时遇到问题，请参阅[排查 Azure 订阅登录问题](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up)。

## <a name="page-hangs-in-the-loading-status"></a>页面在加载状态下挂起

如果 Internet 浏览器页面挂起，请尝试执行以下每个步骤，直到能够访问 Azure 门户为止。

- 刷新页面。
- 使用另一个 Internet 浏览器。
- 使用浏览器的隐私浏览模式：

   - **Microsoft Edge：** 打开“设置”（配置文件图片旁边的三个点），选择“新建 InPrivate 窗口”，然后浏览并登录到 [Azure 门户](https://portal.azure.com/)或 [Azure 帐户中心](https://account.azure.com/Subscriptions) 。 
   - **Chrome：** 选择“Incognito”模式。
   - **Safari：** 选择“文件”，然后选择“新建隐私窗口” 。

- 清除缓存并删除 Internet Cookie：

   - **Microsoft Edge：** 打开“设置”，然后选择“隐私和服务” 。 按照“清除浏览数据”中的步骤操作。 确认选中了“浏览历史记录”、“下载历史记录”和“缓存的图像和文件”复选框，然后选择“删除”   。
   - **Chrome：** 选择“设置”，在“隐私和安全”下选择“清除浏览数据”  。

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>用户以其他用户身份自动登录

如果在 Internet 浏览器中使用多个用户帐户，则可能出现此问题。

若要解决此问题，请尝试下列方法：

- 清除缓存并删除 Internet Cookie。

   - **Microsoft Edge：** 打开“设置”，然后选择“隐私和服务” 。 按照“清除浏览数据”中的步骤操作。 确认选中了“浏览历史记录”、“下载历史记录”、“Cookies”和“缓存的图像和文件”复选框，然后选择“删除”    。
   - **Chrome：** 选择“设置”，在“隐私和安全”下选择“清除浏览数据”  。
- 将浏览器设置重置为默认设置。
- 使用浏览器的专用浏览模式。 
   - **Microsoft Edge：** 打开“设置”（配置文件图片旁边的三个点），选择“新建 InPrivate 窗口”，然后浏览并登录到 [Azure 门户](https://portal.azure.com/)或 [Azure 帐户中心](https://account.azure.com/Subscriptions) 。 
   - **Chrome：** 选择“Incognito”模式。
   - **Safari：** 选择“文件”，然后选择“新建隐私窗口” 。

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>可以登录，但看到错误“找不到任何订阅”

如果选择的目录不正确，或者帐户没有足够的权限，则会出现此问题。

**场景 1：** 登录到 [Azure 门户](https://portal.azure.com/)时收到错误

解决此问题：

- 选择右上角的帐户，确认是否选择了正确的 Azure 目录。
- 如果已选择正确的 Azure 目录，但仍收到错误消息，请将帐户[添加为所有者](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator)。

**场景 2：** 登录到 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)时收到错误

请检查使用的帐户是否是帐户管理员。 要验证谁是帐户管理员，请执行下列步骤：

1.  登录到 [Azure 门户中的订阅视图](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1.  选择要检查的订阅，并选择“设置”。
1.  选择“属性”。 订阅的帐户管理员会显示在“帐户管理员”框中。

## <a name="additional-help-resources"></a>其他帮助资源

Azure 计费和订阅的其他疑难解答文章

- [银行卡被拒绝](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [订阅注册问题](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up)
- [找不到任何订阅](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [企业成本视图已禁用](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
- [Azure 计费文档](https://docs.microsoft.com/azure/cost-management-billing/)

## <a name="contact-us-for-help"></a>联系我们以获取帮助

如有任何疑问或需要帮助，请[创建支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
