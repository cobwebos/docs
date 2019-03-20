---
title: 云市场的潜在顾客管理 | Azure 市场和 AppSource
description: 有关向 Azure 市场和 AppSource 发布产品/服务和技术项目的各种主题的概述
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/05/2018
ms.author: yijenj
ms.openlocfilehash: 810298fc45becf132da6f082df7ad33e7af828aa
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735535"
---
# <a name="lead-management-for-cloud-marketplace"></a>云市场的潜在顾客管理


任何发展良好的业务都以客户为中心。 在当今的产品采购转型中，营销人员需要注重直接与客户进行联系并建立关系。 所以，生成高质量的销售线索对销售周期非常重要。 在[云合作伙伴门户](https://cloudpartner.azure.com/)中列出产品/服务后，可使用工具在客户表示对产品感兴趣或在市场中部署产品后立即以编程方式接收客户联系信息。 



## <a name="what-are-leads-in-the-marketplace"></a>什么是市场中的潜在顾客？

潜在顾客是对市场中的产品感兴趣或部署产品的客户。 无论产品发布在 Azure 市场还是 AppSource，只要在云合作伙伴门户的列表中通过 CRM 恰当设置，就可以接收潜在顾客信息 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>如何将 CRM 系统与云合作伙伴门户连接起来

为了获取潜在顾客信息，云合作伙伴门户上的潜在顾客管理连接器设计为可将 CRM 信息轻松插入可用的 CRM 系统列表中。 现在，可以轻松利用由市场生成的销售线索，而不需要执行重大的工程工作来与外部系统进行集成。

以下是有关如何连接每个可能的潜在顾客目标的分步说明：

**Dynamics CRM Online** - [单击此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)获取有关如何配置 Dynamics CRM Online 以获取潜在顾客的说明。

**Marketo** - [单击此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo)获取有关设置 Marketo 潜在顾客配置以获取潜在顾客的说明。

**Salesforce** - [单击此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce)获取有关设置 Salesforce 实例以获取潜在顾客的说明。

**Azure 表** - [单击此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table)以获取有关设置 Azure 存储帐户以在 Azure 表中获取潜在顾客的说明。

**Https 终结点** - [单击此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https)获取有关设置 Https 终结点以获取潜在顾客的说明。

在正确配置销售线索目标并在产品/服务上点击“发布”后，我们会验证连接并向你发送一个测试销售线索。 如果要在投入使用前查看产品/服务，还可以通过亲自尝试在预览环境中购买产品/服务来测试销售线索连接。 请务必确保销售线索设置保持最新状态，以便你不会错失任何销售线索，因此，每当终端发生了更改时都务必更新这些连接。


### <a name="what-are-the-next-steps"></a>后续步骤有哪些？

在技术性设置到位后，应当将这些销售线索纳入到当前的销售和市场营销策略以及操作流程中。 我们希望更好地了解整个销售流程并希望更紧密地与你合作来提供高质量的潜在顾客和足够的数据来助你成功。 我们欢迎你提供有关如何优化发送给销售线索，以及如何使用额外的数据对其进行增强的反馈，以便帮助客户取得成功。 如果想要提供反馈和建议来使销售团队借助市场潜在顾客取得更大的成功，请告知我们。



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>在云合作伙伴门户上发布期间的常见潜在顾客配置错误 

**无法将潜在顾客保存到 Dynamics CRM。检查 Dynamics CRM 帐户设置。LastCRMError：无法登录 Dynamics CRM，LastCRMException:** 

> 如果选择了 O365 身份验证，请检查用户帐户和密码是否有效。 如果选择了 AAD，请检查租户 ID、应用程序 ID 和应用程序密钥是否与 AAD 上设置的项相匹配。 按照[此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)的说明操作。如果帐户用户名/密码有效，请确保其可以访问 Dynamics 365 并已分配许可证（如果使用 Office 用户，使用 Azure Active Directory 或安全设置，则执行步骤 11-15）。 

 
**无法将潜在顾客保存到 Dynamics CRM。用户不具有潜在顾客实体中 leadsourcecode 属性的创建权限** 

> 应用程序/用户缺少 Microsoft 市场潜在顾客编写器的安全角色。 如果在[此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)使用 Office用户，使用 Azure Active Directory 或安全设置，请执行步骤 11-15。

**无法使用 AAD 将潜在顾客保存到 Dynamics CRM。异常::找不到租户。如果该租户没有活动订阅，可能会发生此实例。**  

> 潜在顾客管理部分中提供的目录 ID 不是有效的目录。 请根据步骤 2 （[此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)的 Azure Active Directory 下）的说明获取目录 ID 

**无法将潜在顾客保存到 Dynamics CRM。LastCRMError：SecLib::RetrievePrivilegeForUser 失败 - 未向用户分配任何角色。**  

> 解决方法：将安全角色分配给 Microsoft 市场潜在顾客编写者。 按照“安全设置”下[此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)的说明进行操作 

**无法使用 AAD 将潜在顾客保存到 Dynamics CRM。异常::在目录中找不到带标识符的应用程序** 

> 潜在顾客管理部分中提供的应用程序 ID 不是有效目录。 请按照步骤 8（[此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)的 Azure Active Directory 下）的说明获取目录 ID。 

**无法使用 AAD 将潜在顾客保存到 Dynamics CRM。异常::请求的租户标识符无效且外部域格式无效** 

> 潜在顾客管理部分中提供的目录 ID 不是有效的目录。 请根据步骤 2（[此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)的 Azure Active Directory 下）的说明获取目录 ID。 

**无法使用 AAD 将潜在顾客保存到 Dynamics CRM。异常::验证凭据时出错：提供的客户端密码无效。** 

> 解决方法：登录 Azure 门户，检查应用程序密钥是否与云合作伙伴门户中的密钥相匹配。 请从[此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)，根据步骤 10（Azure Active Directory 下）的说明生成密码。 

**无法将潜在顾客保存到 Dynamics CRM。LastCRMError：请求通道在等待回复时，00:02:00 后超时。增加传递给 Request 调用的超时值或增加 Binding 上的 SendTimeout 值。分配给此操作的时间可能是较长超时的一部分。**  

> 解决方法：登录云合作伙伴门户，查看“店面详细信息”>>“潜在顾客目标”>>“URL”，检查它是否为有效的 Dynamic CRM 实例

## <a name="frequently-asked-questions"></a>常见问题

**什么是潜在顾客？为什么说他们对在市场上作为发布者的我很重要？** 

潜在顾客是从市场部署产品的客户。 无论你的产品是出现在 [Azure 市场](https://azuremarketplace.microsoft.com/en-us)还是 [AppSource](https://appsource.microsoft.com/) 上，如果已在产品/服务中设置了潜在顾客目标，你将能收到对产品感兴趣的潜在顾客信息。  


**在何处可获得设置潜在顾客目标的帮助？** 

可在此处找到文档：[获取潜在客户](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)或通过 aka.ms/marketplacepublishersupport 提交支持票证以选择产品/服务类型和潜在顾客管理。 



**是否必须在配置潜在顾客目标后，才能在市场上发布产品/服务？**

是的，如果要发布“与我联系”SaaS 应用程序或咨询服务。  
 


**如何确认潜在顾客配置是否正确？**

设置产品/服务和潜在顾客目标后，发布产品/服务。 在潜在顾客验证步骤中，市场会将测试潜在顾客发送到产品/服务中配置的潜在顾客目标。 


**如何查找测试潜在顾客？**


在潜在顾客目标中搜索“MSFT_TEST”，以下是测试潜在顾客数据示例： 

company = MSFT_TEST_636573304831318844 

country = US 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

encoding = UTF-8 

encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name> 

oid = 00Do0000000ZHog 

phone = 1234567890 

title = MSFT_TEST_636573304831318844 

 

**我有实时产品/服务，但是看不到任何潜在顾客？**

每个潜在顾客都会在所选潜在顾客目标的字段中传递数据，潜在顾客将采用以下格式：**源 - 操作|产品/服务** 

  源：

    “AzureMarketplace”, 
    “AzurePortal”, 
    “TestDrive”,  
    “SPZA” (acronym for AppSource) 

  操作：

    “INS” – Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    “PLT” – Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    “DNC” – Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    “Create” – This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    “StartTestDrive” – This is for Test Drives only and is whenever a customer starts their test drive. 


  产品/服务：

    “checkpoint.check-point-r77-10sg-byol”, 
    “bitnami.openedxcypress”, 
    “docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a” 

 

  以下为客户信息示例数据

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

在[潜在顾客信息](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)下了解详细信息。 


**我已将 Azure BLOB 配置为我的潜在顾客目标，为什么看不到潜在顾客？** 

仅当选择 Azure BLOB 存储作为潜在顾客目标时，才会写入潜在顾客。 切换到 Azure 表以实时获取潜在顾客 


**我收到了来自市场的电子邮件，为什么找不到 CRM 中的潜在顾客？**  

最终用户的电子邮件域可能来自 .edu。 出于隐私原因，我们不会从 .edu 域传递 PII 数据。 请通过 aka.ms/marketplacepublishersupport 提交支持票证 


 **已将 Azure Table/Azure BLOB 配置为潜在顾客目标，如何查看潜在顾客？** 

可从 Azure 门户访问 blob 或表，也可以免费下载和安装 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)以查看 Azure 存储帐户的表/blob。 


**已将 Azure 表配置为潜在顾客目标，可在市场发送新潜在顾客时收到通知吗？** 

可以，请按照说明在[此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table)的文档上设置 Azure 表 + 函数。 



**已将 Salesforce 配置为潜在顾客目标，为什么找不到潜在顾客？** 

检查潜在顾客窗体的 Web 是否是基于选择列表的必填字段。 如果是，请将字段切换为非必填文本字段。  
 

**潜在顾客目标存在问题，我丢失了一些潜在顾客。可通过电子邮件将他们发送给我吗？** 

由于 PII（个人标识信息）政策，我们无法通过不安全的电子邮件共享潜在顾客信息。 



**我已将 Azure 存储（BLOB/表）配置为潜在顾客目标，需要多少费用？** 

潜在顾客常规数据较低（几乎所有发布者都 <1 GB）。 费用将取决于收到的潜在顾客数量，如果一个月收到 1,000 个潜在顾客，则费用约为 50 美分。 
