---
title: "在 Azure 安全中心提供安全联系人详细信息 | Microsoft 文档"
description: "本文档演示如何在 Azure 安全中心中提供安全联系人详细信息。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f4ae3395c44f175603556711bfe2f90f0f987c94


---
# <a name="provide-security-contact-details-in-azure-security-center"></a>在 Azure 安全中心提供安全联系人详细信息
Azure 安全中心建议对 Azure 订阅提供安全联系人的详细信息（如果尚未提供）。 如果 Microsoft 安全响应中心 (MSRC) 发现用户的客户数据被某方非法访问或未经授权访问，Microsoft 会使用该信息联系用户。 MSRC 会执行 Azure 网络和基础结构的选择安全监视，并接收来自第三方的威胁情报和恶意投诉。

每日第一个警报发生时会发送电子邮件通知（仅对于高严重级别的警报）。 只能对订阅策略配置电子邮件首选项。 订阅内的资源组将继承这些设置。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  这并非一份循序渐进的指南。
>
>

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡上，选择“提供安全联系人详细信息”。
   ![提供安全联系人][1]
2. 这会打开“提供安全联系人详细信息”边栏选项卡。 选择要对其提供联系人信息的 Azure 订阅。
   ![提供安全联系人详细信息][2]
3. 会再打开一个“提供安全联系人详细信息”边栏选项卡。

   * 输入安全联系人的电子邮件地址或地址，用逗号隔开。 输入的电子邮件地址数量无限制。
   * 输入一个安全联系人的国际电话号码。
   * 若要接收有关于高严重级别警报的电子邮件，请打开选项“通过电子邮件向我发送警报”。
   * 以后，可选择向订阅所有者发送电子邮件通知。 当前此选项处于灰色状态。
   * 选择“确定”将安全联系人信息应用到订阅。

## <a name="see-also"></a>另请参阅
若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md)了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) -- 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md)查找有关使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/)获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png



<!--HONumber=Dec16_HO2-->


