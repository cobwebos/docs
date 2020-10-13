---
title: 潜在客户管理问题和故障排除-Microsoft 合作伙伴中心
description: 了解在合作伙伴中心配置商业市场主管时的常见错误和问题
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: b88c5d7692efa64349f9dbb01b2d4645ec0eb366
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654117"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>潜在客户配置的常见问题和故障排除

本文解答了有关针对您的商业 marketplace 产品/服务的潜在客户的一些常见问题解答。 它还解决了在合作伙伴中心 (CRM) 系统配置潜在客户关系时可能遇到的错误。

## <a name="common-questions-about-lead-management"></a>有关潜在客户管理的常见问题

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>可以在哪里获取有关设置潜在客户目标的帮助？

若要了解如何将 CRM 系统连接到商业 marketplace 产品/服务，请参阅 [商业 marketplace 产品/服务的客户领导](partner-center-portal/commercial-marketplace-get-customer-leads.md) 。 如果有错误，请查看下面的故障排除指南。 若要获得更多支持，请通过 [合作伙伴中心帮助和支持](https://aka.ms/marketplacepublishersupport)提交支持票证。 然后选择“产品/服务创建” > “产品/服务的类型” > “潜在客户管理配置”。

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>是否必须在配置潜在客户目标后，才能在商业市场上发布产品/服务？

答案取决于要发布的产品/服务的类型。 软件即服务 (SaaS) 和 Dynamics 365 Customer Engagement 使用“与我联系”列出所有 Dynamics 365 for Finance and Operations 产品/服务、所有 Dynamics 365 Business Central 产品/服务以及所有咨询服务的产品/服务。 因此，它们需要与潜在客户目标建立连接。 如果你的产品/服务类型未列出，则不需要与潜在客户目标建立连接。 建议你配置潜在客户目标，以免错过商机。

#### <a name="how-can-i-find-the-test-lead"></a>如何查找测试潜在客户？

在潜在客户目标中搜索 `"MSFT_TEST"`。 下面是 Microsoft 的示例测试线索。 请注意，测试主管的格式根据潜在顾客目标的不同而异。

```
{
    "UserDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>我有实时产品/服务，但是为什么看不到任何潜在客户？

请确保与潜在客户目标的连接有效。 在合作伙伴中心选择和产品/服务对应的“发布”后，我们将向你发送测试潜在客户。 如果看到测试潜在客户，则连接有效。 还可以通过在预览步骤中尝试获取产品/服务预览版来测试潜在客户连接。 在商业市场的列表中选择“立即获取”、“与我联系”或“免费试用版”。

此外，请确保正在查找正确的数据。 有关发送到潜在客户目标的潜在客户数据的说明，请参阅 [了解潜在客户数据](partner-center-portal/commercial-marketplace-get-customer-leads.md) 。

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>我已将 Azure Blob 存储配置为我的潜在客户目标，但为什么看不到潜在客户？

目前已不再支持将 Azure Blob 存储作为潜在客户目标，因此你会错过你的产品/服务生成的任何潜在客户。 切换到任何其他[潜在客户目标选项](partner-center-portal/commercial-marketplace-get-customer-leads.md)。 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>我收到了来自商业市场的电子邮件，但为什么在 CRM 中找不到潜在客户？

最终用户的电子邮件域可能来自 .edu。 出于隐私原因，我们不会传递 .edu 域中的个人信息。 请通过[合作伙伴中心帮助和支持](https://aka.ms/marketplacepublishersupport)提交支持票证。

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>我已将 Azure 表配置为我的潜在客户目标。 如何查看潜在客户？

可以从 Azure 门户访问存储在 Azure 表中的潜在客户数据。 也可以免费下载和安装 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)以查看 Azure 存储帐户的表数据。

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>我已将 Azure 表配置为我的潜在客户目标。 在系统发送新的商业市场潜在客户时，我是否能收到通知？

是的。 按照[使用 Azure 表配置潜在客户管理](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md)中的说明进行操作以设置 Microsoft flow，用于在将潜在客户添加到 Azure 表时发送电子邮件。

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>我已将 Salesforce 配置为潜在客户目标，但为什么找不到潜在客户？

检查潜在 web-to-lead 表单是否是基于选择列表的必填字段。 如果是，请将该字段切换为非必填文本字段。

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>潜在客户目标存在问题，我丢失了一些潜在客户。 可以通过电子邮件将他们发送给我吗？

由于个人信息政策，我们无法通过不安全的电子邮件共享潜在客户信息。

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>我已将 Azure 表配置为我的潜在客户目标。 它的成本是多少？

潜在客户生成数据量很低。 对于几乎所有发布者都小于 1 GB。 具体成本取决于接收到的潜在客户数量。 例如，如果每个月收到 1,000 个潜在客户，则成本大约为 50 美分。 有关存储定价的详细信息，请参阅 [Azure 存储概述 - 定价](https://azure.microsoft.com/pricing/details/storage/)。

如果你的问题未得到解答，请通过[合作伙伴中心帮助和支持](https://aka.ms/marketplacepublishersupport)联系 Microsoft 支持部门。 然后选择“产品/服务创建” > 产品/服务的类型” > “潜在客户管理配置”。

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>收到新的潜在客户时，我还收到电子邮件通知。 如何将其他人配置为这些电子邮件的接收者？

在合作伙伴中心访问你的产品/服务，进入“产品/服务设置”页>“潜在客户管理” > “编辑”。 更新“联系人电子邮件”字段下的电子邮件地址。

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> 潜在客户配置错误疑难解答

**无法将潜在顾客保存到 Dynamics CRM。检查 Dynamics CRM 帐户设置。LastCRMError：无法登录 Dynamics CRM，LastCRMException:** 

> 如果选择了 Microsoft 365 身份验证，请检查用户帐户和密码是否有效。 如果选择 Azure Active Directory，请检查租户 ID、应用程序 ID 和应用程序密钥是否与 Azure Active Directory 上设置的内容相匹配。 请按照[此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)的说明进行操作。 如果帐户用户名/密码有效，请确保可以访问 Dynamics 365 并已分配许可证（如果使用 Azure Active Directory，则执行步骤 11-15，或者如何使用 Office 用户，则使用安全设置）。 

**无法将潜在顾客保存到 Dynamics CRM。用户不具有潜在顾客实体中 leadsourcecode 属性的创建权限** 

> 应用程序/用户缺少 Microsoft 市场潜在顾客编写器的安全角色。 如果使用 Azure Active Directory 或安全设置（如果 [在此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)使用 Office 用户），请执行步骤11-15。

**无法使用 AAD 将潜在顾客保存到 Dynamics CRM。异常::找不到租户。如果该租户没有活动订阅，可能会发生此实例。**  

> 潜在顾客管理部分中提供的目录 ID 不是有效的目录。 请根据 [此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)Azure Active Directory) 下步骤 2 (说明获取目录 Id。

**无法将潜在顾客保存到 Dynamics CRM。LastCRMError：SecLib::RetrievePrivilegeForUser 失败 - 未向用户分配任何角色。**  

> 解决方法：将安全角色分配给 Microsoft 市场潜在顾客编写者。 按照[此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)“安全设置”下的说明进行操作。

**无法使用 AAD 将潜在顾客保存到 Dynamics CRM。异常::在目录中找不到带标识符的应用程序** 

> 潜在顾客管理部分中提供的应用程序 ID 不是有效目录。 请按照步骤 8（[此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)的 Azure Active Directory 下）的说明获取目录 ID。 

**无法使用 AAD 将潜在顾客保存到 Dynamics CRM。异常::请求的租户标识符无效且外部域格式无效** 

> 潜在顾客管理部分中提供的目录 ID 不是有效的目录。 请根据步骤 2（[此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)的 Azure Active Directory 下）的说明获取目录 ID。 

**无法使用 AAD 将潜在顾客保存到 Dynamics CRM。异常::验证凭据时出错：提供的客户端密码无效。** 

> 解决方法：登录 Azure 门户，检查应用程序密钥是否与合作伙伴中心的密钥相匹配。 请从[此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)，根据步骤 10（Azure Active Directory 下）的说明生成密码。 

**无法将潜在顾客保存到 Dynamics CRM。LastCRMError：请求通道在等待回复时，00:02:00 后超时。增加传递给 Request 调用的超时值或增加 Binding 上的 SendTimeout 值。分配给此操作的时间可能是较长超时的一部分。**  

> 解决方法：登录到合作伙伴中心，选中 "产品/服务 >> 客户主管 >> URL"，检查它是否为有效的动态 CRM 实例。

