---
title: 在发布门户中设置应用程序 | Microsoft Docs
description: 有关如何在云发布门户中设置应用程序的说明。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 7b5bb0bf8ff5fac10c47cf5bdf9564903f4d6b94
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448477"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>在发布门户中设置应用程序

现在，你已准备好在发布门户中设置应用程序。

## <a name="login-and-create-a-new-offer"></a>登录并创建新套餐

1. 登录[云合作伙伴平台](http://cloudpartner.azure.com/)。
2. 从左侧导航栏中单击“+ 新建套餐”，然后选择“Dynamics 365 for Customer Engagement”。

![选择新套餐](./media/CRMScreenShot14.png)

3. 新套餐“编辑器”视图现在可供使用，我们随时可以开始创作。

![“新建套餐”屏幕](./media/CRMScreenShot15.png)

4. 需要填写的“窗体”显示在“编辑器”视图中的左侧。 每个“窗体”都包含一组要填写的字段。必填字段标有红色的星号 (\*)。

有四个主窗体用于创作 Dynamics 365 for Customer Engagement 套餐

* 产品/服务设置
* 技术信息
* 店面详细信息
* 联系人

## <a name="fill-out-the-offer-settings-form"></a>填写“套餐设置”窗体

产品/服务设置表单是用于指定产品/服务设置的基本表单。 不同的字段如下所述。

### <a name="offer-id"></a>套餐 ID

这是发布者个人资料内产品/服务的唯一标识符。 此 ID 将显示在产品 URL 中。 它只能由小写字母数字字符或短划线 (-) 组成。 ID 不能以短划线结束，并且不能超过 50 个字符。 此字段在套餐推出后处于锁定状态。

例如，如果发布者 **“contoso”** 创建了套餐 ID 为 **“sample-WebApp”** 的套餐，该套餐将以“https://appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview”形式显示在 AppSource 中

### <a name="publisher-id"></a>发布者 ID

通过此下拉列表，可以选择要在其下发布此产品/服务的发布者个人资料。 此字段在套餐推出后处于锁定状态。

### <a name="name"></a>名称

这是产品/服务的显示名称。 这是显示在 [AppSource](https://appsource.microsoft.com/) 中的名称。 该名称不能超过 50 个字符。

单击“保存”以保存进度。 下一步是为套餐添加技术信息。

## <a name="fill-out-the-technical-info-form"></a>填写“技术信息”窗体


在技术信息窗体中填写特定于 Dynamics 365 for Customer Engagement 解决方案的信息。 将鼠标悬停在窗体上会显示更多信息。 请参阅以下示例。

![“技术信息”屏幕](./media/CRMScreenShot16.png)

### <a name="application-info"></a>应用程序信息

大多数发布者将分别在这些字段中保留默认值、选择“用户”、“否”、“否”和空白的应用程序配置 URL，如上面的屏幕截图所示。

### <a name="crm-package"></a>CRM 包

![CRM 包信息](./media/CRMScreenShot17.png)

下面是这些字段的说明：

* 包的文件名：在上述步骤中创建 CRM AppSource 包的 zip 文件时创建的文件名。 在上述示例中，文件名为“Microsoft\_SamplePackage.zip”。
* 包位置的 URL：这是包含上面指定的包文件名的 Azure 存储帐户的 URL。 它是在上述部分的“步骤 9”中创建的 URL。
* 包文件中是否有多个 crm 包：仅当支持多个版本的、包含不同包的 CRM 时，才选择“是”。 大多数合作伙伴会选择“否”。 如果选择“是”，则需要为每个版本的解决方案创建 AppSource 包。 _注意：这并非询问是否存在多个 **zip** 文件。如果有多个 solution.zip 文件，但只有一个版本，则仍应选择“否”。打包工具会自动将这些文件合并到一起。_

### <a name="crm-package-availability"></a>CRM 包可用性

在此部分，选择可以使用该包的 CRM 区域。 有关哪些区域为哪些国家/地区提供服务的信息，请参阅以下链接：[http://o365datacentermap.azurewebsites.net/](http://o365datacentermap.azurewebsites.net/)

注意：部署到德国主权云和 US Gov 主权云需要特殊权限，且在认证期间需要验证

## <a name="storefront-details"></a>店面详细信息

### <a name="offer-summary"></a>产品/服务摘要

这是套餐的价值主张摘要。 它将显示在套餐的搜索页上。 此内容不应超过 100 个字符。

### <a name="offer-description"></a>产品/服务描述

这是显示在应用详细信息页上的说明。 允许的最大长度为 1300 个字符。

### <a name="industries"></a>行业

选择应用最适合的行业。 如果应用与多个行业相关，可将此字段留空。

### <a name="categories"></a>类别

选择与应用相关的类别。 最多选择 3 个类别。

### <a name="app-type"></a>应用类型

选择应用在 AppSource 中启用的试用类型。 “免费”表示应用是免费的。 “试用”表示客户可以在 AppSource 中试用该应用较短的一段时间。 Dynamics 365 for Customer Engagement 应用不支持“请求试用”。 请不要选择此选项。

### <a name="help-link-for-your-app"></a>应用的帮助链接

输入提供应用帮助相关信息的页面的 URL。

### <a name="supported-countriesregions"></a>支持的国家/地区

此字段确定套餐可供试用的国家/地区。

### <a name="supported-languages"></a>支持的语言

选择应用支持的语言。 如果应用还支持此列表中未列出的其他语言，请继续发布套餐，并向 [appsource@microsoft.com](mailto:appsource@microsoft.com) 发送电子邮件以告诉我们。

### <a name="app-version"></a>应用版本

输入应用的版本号

### <a name="app-release-date"></a>应用发布日期

输入应用的发布日期

### <a name="products-your-app-works-with-max-3"></a>可与你的应用配合使用的产品（最多 3 个）

可与你的应用配合使用的产品（仅限列表中的产品）。 最多可以列出三个产品。 若要列出产品，请单击“新建”旁边的加号，此时会创建一个新的文本字段，请在其中输入可与应用配合使用的产品名称。

### <a name="search-keywords-max-3"></a>搜索关键字（最多 3 个）

AppSource 允许客户基于关键字执行搜索。 可以输入关键字集，搜索时会根据这些关键字向用户显示你的应用程序。

例如，如果应用程序名称为“我的电子邮件服务”，则“电子邮件”、“邮件”和“邮件服务”可以是关键字。 请选择用户在 AppSource 搜索框中搜索应用时可能使用的字词。

### <a name="hide-key"></a>隐藏密钥

这是与套餐预览 URL 结合使用的、以便在公共视图中隐藏该套餐的密钥。 这并非密码。 可在此处输入任意字符串。

### <a name="offer-logo-png-format-48x48"></a>套餐徽标（png 格式，48x48）

此徽标将显示在应用的搜索页上。 **只允许 png 格式。** 上传分辨率为 48\*48 像素的 png 图像

### <a name="offer-logo-png-format-216x216"></a>套餐徽标（png 格式，216x216）

此徽标将显示在应用的详细信息页上。 **只允许 png 格式。** 上传分辨率为 216\*216 像素的 png 图像

### <a name="videos"></a>视频

最多可上传 4 个视频。 对于要上传的每个视频，需要填写视频名称、URL（仅限 YouTube 或 Vimeo），以及与视频关联的缩略图。 缩略图必须采用 png 格式，分辨率必须为 1280\*720 像素。 若要添加新视频，请单击加号。 视频缩略图将显示在应用的详细信息页上。

### <a name="documents"></a>文档

最多可以上传三个 PDF 格式的文档。 对于要上传的每个文档，需要填写文档名称，然后上传文档。 文档必须采用 pdf 格式。

若要添加新文档，请单击加号。

### <a name="screenshots"></a>屏幕截图

这些屏幕截图将显示在应用的 AppSource 详细信息页上。

### <a name="privacy-policy"></a>隐私政策

输入应用隐私政策的 URL

### <a name="terms-of-use"></a>使用条款

输入应用的使用条款。 AppSource 客户必须接受这些条款才能试用应用。

### <a name="support-url"></a>支持 URL

输入应用的支持 URL。

### <a name="lead-destination"></a>潜在顾客目标

选择用于存储潜在顾客的 CRM 系统。 如果有以下 CRM 系统之一，在此处请选择“Azure 表”：Salesforce、Marketo、Microsoft Dynamics CRM。 在 AppSource 中试用应用的最终用户（潜在顾客）的详细信息将写入到此处所选的 CRM 系统。 根据选择的 CRM 系统，单击以下相应 URL，以获取有关如何完成下一组字段的信息

* [Azure 表](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>店面详细信息

联系人详细信息仅用于合作伙伴与 Microsoft 之间的内部通信。 注意：必须在这些字段中使用受监视的电子邮件地址。 我们将使用此电子邮件来告知 AppSource 发布进度。 客户只会看到支持 URL。
