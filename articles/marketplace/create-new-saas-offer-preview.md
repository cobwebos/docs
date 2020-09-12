---
title: 如何在 Microsoft 商业应用商店中添加 SaaS 产品/服务的预览观众
description: 如何在 Microsoft 合作伙伴中心为软件即服务 (SaaS) 提供添加预览版观众。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: ed0c7ef98e70774350c9a3ff12b0cc3f4186e1bb
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380681"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>如何添加 SaaS 产品/服务的预览受众

当你在合作伙伴中心创建 "软件即服务" (SaaS) 产品/服务时，你需要定义预览观众，他们可以在现场之前查看你的产品/服务列表。 本文介绍如何配置预览受众。

> [!NOTE]
> 如果选择单独处理事务，则不会看到此选项。 相反，请跳到 [如何推销 SaaS 产品/服务](create-new-saas-offer-marketing.md)。

## <a name="define-a-preview-audience"></a>定义预览受众

在 " **预览受众** " 选项卡上，你可以定义可查看 SaaS 产品/服务的受限受众，然后将其发布到更广泛的 marketplace 群体。 可以将邀请发送到 Microsoft 帐户 (MSA) 或 Azure Active Directory (Azure AD) 电子邮件地址。 手动添加最少1个和最多10个电子邮件地址，或使用 .csv 文件最多导入20个。 你可以随时更新预览受众列表。

> [!NOTE]
> 预览受众不同于专用受众。 预览版用户在在线商店中发布之前，允许其访问你的产品/服务。 你也可以选择创建一个计划并仅面向专用受众提供。 [如何为 SaaS 产品/服务创建计划](create-new-saas-offer-plans.md)中介绍了私有计划。

### <a name="add-email-addresses-manually"></a>手动添加电子邮件地址

1. 在 " **预览受众** " 页上，在提供的框中添加单个 AZURE AD 或 MSA 电子邮件地址和可选说明。
1. 若要添加其他电子邮件地址，请选择 " **添加其他电子邮件** " 链接。
1. 选择 " **保存草稿** "，然后继续下一个选项卡： **技术配置**。
1. 继续 [介绍如何添加 SaaS 产品的技术详细信息](create-new-saas-offer-technical.md)。

### <a name="add-email-addresses-using-the-csv-file"></a>使用 CSV 文件添加电子邮件地址

1. 在 " **预览观众** " 页上，选择 " **导出访问群体 (csv) ** " 链接。
1. 打开。应用程序（例如 Microsoft Excel）中的 CSV 文件。
1. 在。"CSV 文件" 的 " **ID** " 列中，输入要添加到预览受众的电子邮件地址。
1. 在 " **说明** " 列中，可以选择性地为每个电子邮件地址添加描述。
1. 在 " **类型** " 列中，将 **MixedAadMsaId** 添加到具有电子邮件地址的每一行。
1. 将该文件另存为。CSV 文件。
1. 在 " **预览受众** " 页上，选择 " **导入访问群体 (csv) ** " 链接。
1. 在 " **确认** " 对话框中，选择 **"是"**。
1. 选择。CSV 文件，然后选择 " **打开**"。
1. 选择 " **保存草稿** "，然后继续下一个选项卡： **技术配置**。

## <a name="next-steps"></a>后续步骤

- [如何添加 SaaS 产品/服务的技术详细信息](create-new-saas-offer-technical.md)
