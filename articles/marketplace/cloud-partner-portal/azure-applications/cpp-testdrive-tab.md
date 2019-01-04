---
title: Azure 应用程序套餐体验版 | Microsoft Docs
description: 如何在 Azure 市场上为 Azure 应用程序套餐配置体验版。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: de4c8aecefce334889c3fa6790c0ba42673896c7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195337"
---
# <a name="azure-applications-test-drive-tab"></a>Azure 应用程序“体验版”选项卡

使用“体验版”选项卡为客户提供试用体验。

## <a name="test-drive-benefits"></a>体验版的好处

为客户营造试用版体验是一个最佳的做法，能够促使他们放心地购买。 在提供的试用版选项中，体验版在生成高质量潜在顾客并提高潜在顾客转化率方面，是最有效的一个选项。

它为客户提供了与产品主要功能和权益相关的自行试用指南，并在实际的实现方案中进行演示。

## <a name="how-a-test-drive-works"></a>体验版的运作模式

潜在客户在市场中搜索并找到你的应用程序。 客户登录并同意接受使用条款。 此时，客户将收到你的预配置环境来试用固定的小时数，同时你可以开发后续要跟踪的优质潜在顾客。 有关详细信息，请参阅[什么是体验版？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>设置体验版

执行以下步骤，以便启用并配置体验版。

### <a name="to-enable-a-test-drive"></a>若要启用体验版，请执行以下操作：

1. 在“新建套餐”下，选择“体验版”选项卡。
2. 在“体验版”下，对“启用体验版”选项选择“是”。

   ![启用体验版](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>若要配置体验版，请执行以下操作：

启用体验版以后，即可填充以下窗体以设置体验版：
  
 - 详细信息
 - 技术配置
 - 体验版部署订阅详细信息

接下来的屏幕截图显示所有体验版窗体。 名称旁边附有星号 (*) 的字段表示必填字段。 

![配置体验版](./media/managed-app-configure-testdrive.png)

下表介绍为托管应用程序设置体验版所需的字段。

|    **字段**       |  **说明**  |
|  ---------------   |  ---------------  |
|      Description              |   介绍可以在体验版上执行的操作 可以使用基本 HTML 标记来设置此说明的格式。 例如，&lt;p&gt;、&lt;em&gt;、&lt;ul&gt;、&lt;li&gt;、&lt;ol&gt;、标题。                |
|    用户手册                |     上传客户进行体验版体验时可以使用的用户手册。 此文档必须是 .pdf 文件。              |
|         体验版演示视频（可选）           |       可以提供体验版的视频演示。 客户在进行体验之前，可以观看此视频。 提供在 YouTube 或 Vimeo 上的视频的 URL。 如果选择“+ 添加视频”，系统会提示你提供以下信息：<ul><li>名称</li><li>代码</li><li>缩略图（PNG 格式，533 x 324 像素）</li></ul>            |
|       Instances             |        配置需要多少个实例、在哪些区域及客户多久可以获得体验版。 有关更多信息，请参阅[如何发布体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/azure-resource-manager-test-drive#how-to-publish-a-test-drive)。           |
|       体验版持续时间（小时）             |       输入一个整数来代表小时数。 允许的范围为 1 到 999。            |
|        体验版 ARM 模板            |        上传一个压缩 (.zip) 文件，其中有适用于应用的 Azure 资源管理模板。 有关详细信息，请参阅 [Azure 资源管理器体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/azure-resource-manager-test-drive)。            |
|        访问信息            |         在客户获取体验版后提供访问信息。 例如，访问体验版的 URL，以及登录信息。 . 可以使用基本 HTML 标记来设置此说明的格式。 例如，&lt;p&gt;、&lt;em&gt;、&lt;ul&gt;、&lt;li&gt;、&lt;ol&gt;、标题。          |
|       访问订阅 ID             |       此项授予对 Azure 服务和 Azure 门户的访问权限。 将在该订阅中报告资源用量和计收服务费用。 如果没有一个专门用于体验版的独立 Azure 订阅，请创建一个订阅。             |
|          Azure AD 租户 ID          |        提供 Azure Active Directory 中的现有租户，或者为此体验版创建一个租户。           |
|         Azure AD 应用 ID           |       创建并注册新的应用程序。 Microsoft 使用此应用程序对体验版实例执行操作。            |
|          Azure AD 应用密钥          |         为应用创建一个身份验证密钥，然后将其粘贴到此字段中。          |

提供全部所需的信息后，选择“保存”，完成体验版的设置。

## <a name="next-steps"></a>后续步骤

[“市场”选项卡](./cpp-marketplace-tab.md)