---
title: include 文件
description: include 文件
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/13/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: f2446a924ceed37c51779efc9d9e94c0252a2067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80321773"
---
通过 **"测试驱动器**"选项卡，您可以设置演示（或"试驾"），使客户能够在承诺购买之前试用您的产品/服务。 在文章"[什么是试驾"中了解更多信息。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) 如果您不想再为产品/服务提供试驾，请返回 **"产品/服务设置**"页面并取消选中**启用试驾**。

### <a name="technical-configuration"></a>技术配置
提供以下类型的测试驱动器，每种驱动器都有自己的技术配置要求。

- [Azure 资源管理器](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [逻辑应用](#technical-configuration-for-logic-app-test-drive)
- [电源 BI（](#technical-configuration-not-required-for-power-bi-test-drives)不需要技术配置）

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure 资源管理器测试驱动器的技术配置

包含构成解决方案的所有 Azure 资源的部署模板。 适合此方案的产品仅使用 Azure 资源。 详细了解如何设置 Azure[资源管理器测试驱动器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

- **区域**（必需）：目前有 26 个 Azure 支持的区域，可以在其中提供您的试驾。 通常，您需要在预期客户数量最多的区域提供试驾，以便他们选择最接近的区域以获得最佳性能。 您需要确保允许订阅部署所选择的每个区域所需的所有资源。

- **实例**：选择类型（热或冷）和可用实例数，这些实例将乘以产品/服务可用的区域数。

**热**：这种类型的实例已部署，并等待每个选定区域的访问。 客户可以立即访问测试驱动器*的热*实例，而无需等待部署。 弊端在于这些实例始终在 Azure 订阅上运行，因此，它们会产生较高的运行时间成本。 强烈建议至少使用一个*热*实例，因为大多数客户不希望等待完全部署，如果没有*热*实例，则会导致客户使用量下降。

**冷**：这种类型的实例表示每个区域可能部署的实例总数。 冷实例要求在整个测试驱动器资源管理器模板在客户请求试驾时进行部署，因此*冷*实例的加载速度比*热*实例慢得多。 权衡是，您只需要在试驾期间付费，它*并不总是*像*使用热*实例那样在 Azure 订阅上运行。

- **试用 Azure 资源管理器模板**：上载包含 Azure 资源管理器模板的 .zip。  在快速入门文章"[使用 Azure 门户创建和部署 Azure 资源管理器模板"](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)中了解有关创建 Azure 资源管理器模板详细信息。

- **试驾持续时间**（必需）：输入试驾将保持活动状态的时间长度（以小时数计）。 此时间段结束后，体验版会自动终止。 此持续时间只能由整个小时数设置（例如"2"小时，"1.5"无效）。

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 试驾的技术配置

Microsoft 可以通过托管和维护使用此类测试驱动器的服务预配和部署来消除设置测试驱动器的复杂性。 无论试驾是面向业务中心、客户参与还是运营受众，此类托管试驾的配置都是相同的。

- **最大并发测试驱动器**（必需）：设置一次可以使用试驾的最大客户数。 每个并发用户将在测试驱动器处于活动状态时使用 Dynamics 365 许可证，因此您需要确保有足够的许可证来支持最大限制集。 建议的值为 3-5。

- **试驾持续时间**（必需）：通过定义小时数输入试驾将保持活动状态的时间长度。 在此多小时后，会话将结束，不再使用您的许可证之一。 根据产品/服务的复杂性，我们建议值为 2-24 小时。 此持续时间只能由整个小时数设置（例如"2"小时，"1.5"无效）。  如果用户时间已用完，并且希望再次访问试驾，用户可以请求新的会话。

- **实例 URL（** 必需）：客户将开始其试驾的 URL。 通常，使用安装示例数据（例如`https://testdrive.crm.dynamics.com`），运行应用的 Dynamics 365 实例的 URL。

- **实例 Web API URL（** 必需）：通过登录到 Microsoft 365 帐户并导航到 **"设置**\&"gt，检索 Dynamics 365 实例的 Web API URL;**自定义**\&gt;**开发人员资源**\&gt;**实例 Web API（服务根 URL），** 复制此处找到的 URL（例如`https://testdrive.crm.dynamics.com/api/data/v9.0`）。

- **角色名称**（必需）：提供您在自定义 Dynamics 365 试驾中定义的安全角色名称。 这将在用户试驾期间（例如，测试-驱动器角色）分配给用户。

#### <a name="technical-configuration-for-logic-app-test-drive"></a>逻辑应用试驾的技术配置

任何自定义产品都应使用这种类型的试驾部署模板，该模板包含各种复杂的解决方案体系结构。 有关设置逻辑应用测试驱动器的详细信息，请访问 GitHub 上的[操作](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)[和客户参与度](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **区域**（必需，单选下拉列表）：目前有 26 个 Azure 支持的区域，可以在其中提供您的试驾。 逻辑应用的资源将部署在您选择的区域中。 如果逻辑应用在特定区域中存储了任何自定义资源，请确保在此处选择该区域。 执行此操作的最佳方法是在门户中的 Azure 订阅中本地完全部署逻辑应用，并在进行此选择之前验证其是否正常工作。

- **最大并发测试驱动器**（必需）：设置一次可以使用试驾的最大客户数。 这些测试驱动器已部署，使客户能够立即访问它们，而无需等待部署。

- **试驾持续时间**（必需）：输入试驾将保持活动状态的时间长度（以小时数计）。 此时间段结束后，试驾将自动终止。

- **Azure 资源组名称**（必需）：输入逻辑应用测试驱动器保存的[Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名称。

- **Azure 逻辑应用名称**（必需）：输入将测试驱动器分配给用户的逻辑应用的名称。 此逻辑应用必须保存在上面的 Azure 资源组中。

- **取消预配逻辑应用名称**（必需）：输入逻辑应用的名称，该应用在客户完成后取消配置试驾。 此逻辑应用必须保存在上面的 Azure 资源组中。

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 测试驱动器不需要技术配置

想要演示交互式 Power BI 视觉对象的产品可以使用嵌入式链接共享自定义仪表板作为试驾，无需进一步的技术配置。 详细了解如何设置[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)模板应用。

### <a name="deployment-subscription-details"></a>部署订阅详细信息

为了代表您部署测试驱动器，请创建并提供单独的唯一 Azure 订阅。 （电源 BI 测试驱动器不需要）。

- **Azure 订阅 ID（Azure**资源管理器和逻辑应用需要）：输入订阅 ID 以授予对 Azure 帐户服务的访问权限，以便进行资源使用情况报告和计费。 如果尚未创建，我们建议您考虑[创建单独的 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)以用于测试驱动器。 您可以通过登录到[Azure 门户](https://portal.azure.com/)并导航到左侧菜单的 **"订阅"** 选项卡来查找 Azure 订阅 ID。 选择该选项卡将显示您的订阅 ID（例如"a83645ac-1234-5ab6-6789-1h234g764ghty"）。

- **Azure AD 租户 ID（** 必需）：输入 Azure 活动目录 （AD）[租户 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，选择左侧菜单中的"活动目录"选项卡，选择 **"属性**"，然后查找列出的**目录 ID**号（例如 50c464d3-4930-494c-963c-1e951d15360e）。 您还可以使用域名 URL 在 ：[https://www.whatismytenantid.com](https://www.whatismytenantid.com)查找组织的租户 ID。

- **Azure AD 租户名称**（动态 365 需要）：输入 Azure 活动目录 （AD） 名称。 要查找此名称，请登录到[Azure 门户](https://portal.azure.com/)，在右上角，租户名称将列在您的帐户名称下。

- **Azure AD 应用 ID（** 必需）：输入 Azure 活动目录 （AD）[应用程序 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，选择左侧菜单中的"活动目录"选项卡，选择**应用注册**，然后查找列出的应用程序**ID**号（例如 50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 应用客户端机密**（必需）：输入 Azure AD 应用程序[客户端密钥](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 要查找此值，请登录到 Azure[门户](https://portal.azure.com/)。 选择左侧菜单中的**Azure 活动目录**选项卡，选择**应用注册**，然后选择试驾应用。 接下来，选择**证书和机密**，选择 **"新建客户端机密**"，输入说明，选择 **"从不在过期**"下，然后选择"**添加**"。 **Never** 请确保向下复制该值。 （执行此操作之前不要离开页面，否则您将无法访问该值。

在继续下一节之前，请记住**保存**！

### <a name="test-drive-listings-optional"></a>试驾列表（可选）

**"试驾****"选项卡下的"试驾列表**"选项显示您的试驾可用的语言（和市场），当前英语（美国）是唯一可用的位置。 此外，此页显示特定于语言的列表的状态以及添加该列表的日期/时间。 您需要为每个语言/市场定义试驾详细信息（说明、用户手册、视频等）。

- **说明**（必需）：描述您的试驾、演示的内容、供用户试验的目标、要浏览的功能以及任何相关信息，以帮助用户确定是否获取您的产品/服务。 此字段中最多可输入 3，000 个字符的文本。 

- **访问信息**（Azure 资源管理器和逻辑测试驱动器所需的信息）：解释客户需要了解哪些内容才能访问和使用此测试驱动器。 演练使用产品/服务的方案，以及客户在整个试驾中访问功能的准确情况。 此字段中最多可输入 10，000 个字符的文本。

- **用户手册**（必需）：对试驾体验的深入演练。 《用户手册》应准确介绍您希望客户从体验试驾中获得什么，并作为他们可能存在的任何问题的参考。 文件必须采用 PDF 格式，并在上载后命名（最多 255 个字符）。

- **视频：添加视频**（可选）：视频可以上传到 YouTube 或 Vimeo，并在此处引用链接和缩略图图像 （533 x 324 像素），以便客户可以查看浏览信息，帮助他们更好地了解试驾，包括如何成功使用您的产品/服务的功能，并了解突出其优势的方案。
  - **名称**（必需）
  - **网址（仅限 YouTube 或 Vimeo）（** 必填）
  - **缩略图 （533 x 324px）**： 图像文件必须采用 PNG 格式。

完成这些字段后选择 **"保存**"。
