---
title: 商业市场铅管理 |Azure 应用商店和应用源
description: 有关向 Azure 市场和 AppSource 发布产品/服务和技术项目的各种主题的概述
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 09b02d043e970c68cdff0e3dc4f97328c9d74c84
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383777"
---
# <a name="lead-management-for-commercial-marketplace"></a>商业市场领导管理


任何发展良好的业务都以客户为中心。 在当今产品收购的转型中，营销人员需要专注于与客户直接建立联系，并建立关系。 所以，生成高质量的销售线索对销售周期非常重要。 在[合作伙伴中心](https://partner.microsoft.com/)列出您的产品/服务后，在客户表示兴趣或在市场上部署产品后，您可以立即以编程方式接收客户联系信息。 



## <a name="what-are-leads-in-the-marketplace"></a>什么是市场中的潜在顾客？

潜在顾客是对市场中的产品感兴趣或部署产品的客户。 无论您的产品是在 Azure 应用商店还是 AppSource 上列出的，一旦从 CRM 正确设置到合作伙伴中心中的列表，您都可以从客户接收潜在客户。 


## <a name="how-to-connect-your-crm-system-with-partner-center"></a>如何将 CRM 系统与合作伙伴中心连接

为了开始获取潜在顾客，合作伙伴中心中的潜在客户管理连接器设计为可以轻松地将 CRM 信息插入可用的 CRM 系统列表。 现在，可以轻松利用由市场生成的销售线索，而不需要执行重大的工程工作来与外部系统进行集成。

以下是有关如何连接每个可能的潜在顾客目标的分步说明：

**动态 CRM 在线**- 有关如何配置动态 CRM Online 以获取潜在顾客的说明，请参阅[Dynamics 365 的配置潜在客户管理](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)。

**Marketo** - 有关设置 Marketo 潜在顾客配置以获得潜在顾客的说明，请参阅[在 Marketo 中配置潜在顾客管理](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)。

**Salesforce** - 有关设置 Salesforce 实例以获取潜在顾客的说明，请参阅[为 Salesforce 配置潜在顾客管理](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)。

**Azure 表**- 有关设置 Azure 存储帐户以在 Azure 表中获取潜在顾客的说明，请参阅[使用 Azure 表配置潜在顾客管理](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md)。

**Https 终结点**- 有关设置 Https 终结点获取潜在顾客的说明，请参阅[使用 HTTPS 终结点配置潜在顾客管理](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md)。

在正确配置销售线索目标并在产品/服务上点击“发布”后，我们会验证连接并向你发送一个测试销售线索。 如果要在投入使用前查看产品/服务，还可以通过亲自尝试在预览环境中购买产品/服务来测试销售线索连接。 请务必确保潜在顾客设置保持最新状态，以便你不会错失任何潜在顾客，因此，每当终端发生了更改时都务必更新这些连接。

### <a name="what-are-the-next-steps"></a>后续步骤有哪些？

技术设置到位后，您应该将这些潜在顾客纳入您当前的销售&营销战略和运营流程。 我们希望更好地了解整个销售流程并希望更紧密地与你合作来提供高质量的潜在顾客和足够的数据来助你成功。 我们欢迎你提供有关如何优化发送给销售线索，以及如何使用额外的数据对其进行增强的反馈，以便帮助客户取得成功。 如果您有兴趣提供反馈和建议，使您的销售团队在市场线索方面更加成功，请告诉我们。

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a>在合作伙伴中心发布期间的常见潜在顾客配置错误

**无法将潜在顾客保存到动态 CRM。检查动态 CRM 帐户设置。上次 CRM 错误：无法登录到 Dynamics CRM，最后一个CRM例外：** 

> 如果选择了 O365 身份验证，请检查用户帐户和密码是否有效。 如果选择了 AAD，请检查租户 ID、应用程序 ID 和应用程序密钥是否与 AAD 上设置的项相匹配。 请按照[此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)的说明进行操作。 如果帐户用户名/密码有效，请确保它有权访问 Dynamics 365 并分配了许可证（如果使用 Azure 活动目录或安全设置（如果使用 Office 用户），则步骤 11-15）。 

**无法将潜在顾客保存到动态 CRM。用户没有潜在顾客实体中潜在顾客源码属性的创建权限** 

> 应用程序/用户缺少 Microsoft 市场潜在顾客编写器的安全角色。 如果在[此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)使用 Office用户，使用 Azure Active Directory 或安全设置，请执行步骤 11-15。

**无法使用 AAD 将潜在顾客保存到动态 CRM。异常：未找到租户。如果租户没有活动订阅，则可能发生此实例。**  

> 潜在顾客管理部分中提供的目录 ID 不是有效的目录。 请根据步骤 2 中的说明（在 Azure 活动目录下，[在此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)获取目录 ID）。

**无法将潜在顾客保存到动态 CRM。上次 CRM 错误：SecLib：：检索用户权限失败 - 未向用户分配任何角色。**  

> 解决方案：将安全角色分配给 Microsoft 市场潜在顾客编写者。 按照"安全[设置"在此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)操作说明。

**无法使用 AAD 将潜在顾客保存到动态 CRM。异常：： 在目录中找不到具有标识符的应用程序** 

> 潜在顾客管理部分中提供的应用程序 ID 不是有效目录。 请按照步骤 8（[此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)的 Azure Active Directory 下）的说明获取目录 ID。 

**无法使用 AAD 将潜在顾客保存到动态 CRM。异常：请求的租户标识符无效，并且外部域格式无效** 

> 潜在顾客管理部分中提供的目录 ID 不是有效的目录。 请根据步骤 2（[此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)的 Azure Active Directory 下）的说明获取目录 ID。 

**无法使用 AAD 将潜在顾客保存到动态 CRM。异常：错误验证凭据。： 提供了无效的客户端密钥。** 

> 解决方法：登录到 Azure 门户，检查应用程序密钥是否与合作伙伴中心中的内容匹配。 请从[此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)，根据步骤 10（Azure Active Directory 下）的说明生成密码。 

**无法将潜在顾客保存到动态 CRM。上次 CRMError： 请求通道超时，等待 00：02：00 后的答复。增加传递到"请求"调用的超时值，或增加绑定上的"发送超时"值。分配给此操作的时间可能是较长超时的一部分。**  

> 解决方法：登录到合作伙伴中心，检查网店详细信息 >> 潜在顾客目标 >> URL，检查其是否有效动态 CRM 实例。

## <a name="frequently-asked-questions"></a>常见问题

**什么是潜在顾客？为什么说他们对在市场上作为发布者的我很重要？** 

潜在顾客是从市场部署产品的客户。 无论你的产品是出现在 [Azure 市场](https://azuremarketplace.microsoft.com)还是 [AppSource](https://appsource.microsoft.com/) 上，如果已在产品/服务中设置了潜在顾客目标，你将能收到对产品感兴趣的潜在顾客信息。  

**在何处可获得设置潜在顾客目标的帮助？** 

您可以在此处找到文档：通过选择产品/服务类型和潜在顾客管理aka.ms/marketplacepublishersupport[获取客户潜在顾客](./partner-center-portal/commercial-marketplace-get-customer-leads.md)或提交支持票证。 

**是否必须在配置潜在顾客目标后，才能在市场上发布产品/服务？**

是的，如果要发布“与我联系”SaaS 应用程序或咨询服务。  
 
**如何确认潜在顾客配置是否正确？**

设置产品/服务和潜在顾客目标后，发布产品/服务。 在潜在顾客验证步骤中，市场会将测试潜在顾客发送到产品/服务中配置的潜在顾客目标。 

**如何查找测试潜在顾客？**

在潜在顾客目标中搜索"MSFT_TEST"，下面是测试线索数据示例： 

company = MSFT_TEST_636573304831318844 

country = US 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

encoding = UTF-8 

encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844]\<优惠名称> 

oid = 00Do0000000ZHog 

phone = 1234567890 

title = MSFT_TEST_636573304831318844 

**我有一个现场报价，但没有看到任何线索？**

每个潜在顾客都会在所选潜在顾客目标的字段中传递数据，潜在顾客将采用以下格式：源 - 操作|产品/服务**** 

  *来源：*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *行动：*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *提供：*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *以下是客户信息的示例数据*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

在[潜在顾客信息](./partner-center-portal/commercial-marketplace-get-customer-leads.md)下了解详细信息。 

**我已将 Azure BLOB 配置为我的潜在顾客目标，为什么看不到潜在顾客？** 

仅当选择 Azure BLOB 存储作为潜在顾客目标时，才会写入潜在顾客。 切换到 Azure 表以实时接收潜在顾客。

**我收到了来自市场的电子邮件，为什么找不到 CRM 中的潜在顾客？**  

最终用户的电子邮件域可能来自 .edu。 出于隐私原因，我们不会从 .edu 域传递个人可识别数据。 提交[支持票证](https://aka.ms/marketplacepublishersupport)。

**已将 Azure Table/Azure BLOB 配置为潜在顾客目标，如何查看潜在顾客？** 

可以从 Azure 门户访问 Blob 或表，也可以免费下载和安装[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)以查看 Azure 存储帐户的表/blob。 

**已将 Azure 表配置为潜在顾客目标，可在市场发送新潜在顾客时收到通知吗？** 

可以，请按照说明在[此处](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md)的文档上设置 Azure 表 + 函数。 

**已将 Salesforce 配置为潜在顾客目标，为什么找不到潜在顾客？** 

检查潜在顾客窗体的 Web 是否是基于选择列表的必填字段。 如果是，请将字段切换为非必填文本字段。  
 
**我的主要目的地有一个问题，我错过了一些线索。我可以在电子邮件中发送给我吗？** 

由于隐私政策，我们无法通过不安全的电子邮件共享潜在顾客信息。 

**我已将 Azure 存储（BLOB/表）配置为潜在顾客目标，需要多少费用？** 

潜在顾客常规数据较低（几乎所有发布者都 <1 GB）。 费用将取决于收到的潜在顾客数量，如果一个月收到 1,000 个潜在顾客，则费用约为 50 美分。 
