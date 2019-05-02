---
title: Dynamics 365 客户参与应用程序提供体验版选项卡 |Azure Marketplace
description: 如何在 AppSource 市场上配置 Dynamics 365 for Customer Engagement 应用程序产品/服务的体验版。
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 5bb5f39ef5f5bce09a8639ba9eedc6d042e60c1d
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942358"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Dynamics 365 for Customer Engagement 应用程序“体验版”选项卡

使用“体验版”选项卡为客户创建试用体验。  它为客户提供了以自己的方式亲手试用产品/服务主要功能和优点的机会，从而在真实的实现场景中展示这些功能及优点。  在提供的试用版选项中，体验版在生成高质量潜在顾客并提高潜在顾客转化率方面，是最有效的一个选项。  有关详细信息，请参阅[什么是体验版？](../test-drive/what-is-test-drive.md)

Dynamics 365 应用程序体验版将作为 Microsoft 托管解决方案自动运行。  有关详细信息，请参阅[托管体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)。

“体验版”选项卡可包含三个部分：“体验版”、“详细信息”和“技术配置”。  启用“体验版”功能后，才会显示后两个部分。  名称旁边附有星号 (*) 的字段表示必填字段。 


## <a name="test-drive-section"></a>“体验版”部分

若要启用此功能，请将“启用体验版”选择为“是”。


## <a name="details-section"></a>“详细信息”部分

“详细信息”部分将提供基本的体验版信息。   

![体验版的“详细信息”部分](./media/test-drive-tab-details.png)

下表介绍为 Dynamics 365 应用程序设置体验版所需的字段。 必填字段用星号 (*) 表示。

|      字段                    |    描述                  |
|    ---------                  |  ---------------                |
|      说明\*            |   介绍可以在体验版上执行的操作 可以使用基本 HTML 标记来设置此说明的格式。 例如，&lt;p&gt;、&lt;em&gt;、&lt;ul&gt;、&lt;li&gt;、&lt;ol&gt;、标题。  |
|  用户手册\*                |   上传客户进行体验版体验时可以使用的用户手册。 此文档必须是 .pdf 文件。 |
|  体验版演示视频（可选） |  可以提供体验版的视频演示。 客户在进行体验之前，可以观看此视频。 提供在 YouTube 或 Vimeo 上的视频的 URL。 如果选择“+ 添加视频”，系统会提示你提供以下信息：<ul><li>名称</li><li>代码</li><li>缩略图（PNG 格式，533 x 324 像素）</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>“技术配置”部分

本部分将提供有关体验版的技术详细信息。

![体验版的“详细信息”部分](./media/test-drive-tab-tech-config.png)

其中的字段都具有以下用途。  必填字段用星号 (*) 表示。

|      字段                    |    描述                  |
|    ---------                  |  ---------------                |
| 测试驱动器的类型\*            | 选择“Microsoft 托管(Dynamics 365 for Customer Engagement)”。  |
| 最大并发测试驱动器\*    | 在任何给定时间点，活跃体验版的并发实例数。 当每个用户的体验版处于活动状态时，该用户将使用一个 Dynamics 许可证，因此，你需要确保至少有这么多的 Dynamics 许可证可供体验版用户使用。 建议的值为 3-5。  |
| 测试驱动器持续时间 （小时）\*   | 用户的体验版实例将处于活动状态的最大小时数。 超过此时限后，将从你的租户中取消设置该实例。 建议值为 2-24 小时，具体取决于应用的复杂性。 用户时间耗尽后若想再次评估，可随时另申请一个体验版。  |
| 实例 URL\*                  | 体验版最初导航到的 URL。 通常情况下，这是 Dynamics 365 实例的 URL，应用和示例数据便安装在该实例上。  |
| Azure AD 租户 ID\*            | Dynamics 365 实例的 Azure 租户 GUID。 若要检索此值，请登录到 Azure 门户并导航到“Azure Active Directory” > “选择属性” > “复制目录 ID”。  |
| Azure AD App ID\*               | Azure AD 应用程序的 GUID  |
| Azure AD App Key\*              | Azure AD 应用程序的机密，例如：`IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` |
| Azure AD 租户名称\*          | Dynamics 365 实例的 Azure 租户名称。 使用 <tenantname.>onmicrosoft.com 格式，例如：`testdrive.onmicrosoft.com`  |
| 实例 Web API URL\*          | Dynamics 365 实例的 Web API URL。 可登录到 Microsoft Dynamics 365 实例，并导航到“设置” > “自定义” > “开发人员资源” > “实例 Web API”（复制此 URL）来检索此值。 示例值： `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| 角色名称\*                     | 为体验版创建的自定义 Dynamics 365 安全角色的名称，并将在用户运行此角色时分配给用户，例如 `testdriveuser`。 |
|  |  |

提供所有必要信息后，选择“保存”。


## <a name="next-steps"></a>后续步骤

接下来，你需要在[“店面详细信息”选项卡](./cpp-storefront-details-tab.md)中提供营销和销售信息。

