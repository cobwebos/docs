---
title: 在 Microsoft 商业应用商店中配置测试驱动器
description: 了解测试驱动器。 在提交购买之前，测试驱动器允许新客户测试您的产品/服务。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/13/2019
ms.openlocfilehash: 8bd17858fa6866549088de8307f40d7b79daef6b
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82786540"
---
# <a name="learn-about-test-drive-for-your-offer"></a>了解产品/服务的试用版

使用 Microsoft 商业应用商店中的 "**测试驱动器**" 选项，您可以配置您的产品关键功能的动手自学指导教程。 使用测试驱动器，新客户可以在提交之前试用你的产品/服务。 有关详细信息，请参阅[什么是测试驱动器？](../cloud-partner-portal/test-drive/what-is-test-drive.md)。

如果你不再希望为你的产品/服务提供一个测试驱动器，请返回 "**产品/服务设置**" 页，然后取消选中 "**启用测试驱动器**"。

## <a name="technical-configuration"></a>技术配置

可以使用以下类型的测试驱动器，每种都有其自己的技术配置要求。

- [Azure 资源管理器](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [逻辑应用](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) （不需要技术配置）

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure 资源管理器测试驱动器的技术配置

有一个部署模板，其中包含组成解决方案的所有 Azure 资源。 这适用于仅使用 Azure 资源的产品。 详细了解如何设置[Azure 资源管理器试用版驱动器](../cloud-partner-portal/test-drive/azure-resource-manager-test-drive.md)。

- **区域**（必需）：当前存在26个支持 Azure 的区域，可在其中提供测试驱动器。 通常情况下，你将希望使你的测试驱动器在你预计的客户数最多的区域中可用，以便他们可以选择最接近的区域以获得最佳性能。 你将需要确保你的订阅可以在你选择的每个区域中部署所需的所有资源。

- **实例**：选择 "类型" （"热" 或 "冷"）和 "可用实例数"，将乘以提供提供的区域数。

  - **热**：此类实例已部署并等待每个选定区域的访问权限。 客户可以即时访问测试驱动器的*热*实例，而无需等待部署。 弊端在于这些实例始终在 Azure 订阅上运行，因此，它们会产生较高的运行时间成本。 强烈建议至少具有一个*热*实例，因为大多数客户不希望等待完全部署，因此，如果没有可用的*热*实例，则会导致客户使用中出现下拉。

  - **冷**：此类型的实例表示每个区域可能部署的实例总数。 冷实例需要整个测试驱动器资源管理器模板在客户请求测试驱动器时进行部署，因此，*冷*实例的加载速度慢于*热*实例。 缺点是，你只需支付测试驱动器的持续时间，就*不*会像使用*热*实例那样始终在 Azure 订阅上运行它。

- **测试驱动器 Azure 资源管理器模板**：上传包含 Azure 资源管理器模板的 .zip。  若要详细了解如何创建 Azure 资源管理器模板，请参阅快速入门文章[使用 Azure 门户创建和部署 azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。

- "**测试驱动器持续时间**（必需）"：输入测试驱动器将保持活动状态的时间长度（以小时为单位）。 此时间段结束后，体验版会自动终止。 此持续时间只能设置为整小时数（例如 "2" 小时，"1.5" 无效）。

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 的技术配置测试驱动器

Microsoft 可以通过使用这种类型的测试驱动器来托管和维护服务预配和部署，消除设置测试驱动器的复杂性。 无论测试驱动器是面向业务中心、客户接洽还是操作受众，此类型的托管测试驱动器的配置都是相同的。

- **最大并发测试驱动器**数（必需）：设置一次可以使用您的测试驱动器的最大客户数。 在测试驱动器处于活动状态时，每个并发用户将使用 Dynamics 365 许可证，因此你将需要确保有足够的可用许可证来支持最大限制集。 建议的值为 3-5。

- **测试驱动器持续时间**（必需）：通过定义小时数来输入测试驱动器保持活动状态的时间长度。 超过此时间后，会话将结束，并且不再使用您的许可证之一。 建议使用2-24 小时的值，具体取决于产品/服务的复杂性。 此持续时间只能设置为整小时数（例如 "2" 小时，"1.5" 无效）。  如果用户运行时间不足，并且想要再次访问测试驱动器，则用户可以请求新的会话。

- **实例 url** （必需）：客户将从其开始测试驱动器的 url。 通常是运行应用并安装示例数据的 Dynamics 365 实例的 URL （例如`https://testdrive.crm.dynamics.com`）。

- **实例 Web api url** （必需）：通过登录到 Microsoft 365 帐户并导航到 "**设置** \&" gt; 来检索 Dynamics 365 实例的 Web API url**自定义** \&g t;**开发人员资源** \&g t;**实例 WEB API （服务根 url）**，复制此处找到的 URL （例如`https://testdrive.crm.dynamics.com/api/data/v9.0`）。

- **角色名称**（必需）：提供自定义 Dynamics 365 测试驱动器中定义的安全角色名称。 这会在其测试驱动器（例如，测试驱动器角色）期间分配给用户。

### <a name="technical-configuration-for-logic-app-test-drive"></a>逻辑应用的技术配置测试驱动器

任何自定义产品都应使用包含各种复杂解决方案体系结构的此类测试驱动器部署模板。 有关设置逻辑应用测试驱动器的详细信息，请访问 GitHub 上的[操作](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)和[客户参与](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **区域**（必需，单项选择下拉列表）：当前有26个支持 Azure 的区域可供使用。 逻辑应用的资源将部署在所选的区域中。 如果逻辑应用包含存储在特定区域中的任何自定义资源，请确保在此处选择 "区域"。 实现此目的的最佳方式是在门户中将逻辑应用完全部署在 Azure 订阅上，并在做出此选择之前验证它是否正常工作。

- **最大并发测试驱动器**数（必需）：设置一次可以使用您的测试驱动器的最大客户数。 已部署这些测试驱动器，从而使客户无需等待部署即可立即访问它们。

- "**测试驱动器持续时间**（必需）"：输入测试驱动器将保持活动状态的时间长度（以小时为单位）。 此时间段结束后，测试驱动器自动终止。

- **Azure 资源组名称**（必需）：输入逻辑应用测试驱动器保存到的[azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名称。

- **Azure 逻辑应用名称**（必需）：输入将测试驱动器分配给用户的逻辑应用的名称。 此逻辑应用必须保存在上述 Azure 资源组中。

- 取消预**配逻辑应用名称**（必需）：在客户完成后，输入取消设置测试驱动器的逻辑应用的名称。 此逻辑应用必须保存在上述 Azure 资源组中。

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 测试驱动器无需技术配置

希望演示交互 Power BI 视觉对象的产品可以使用嵌入的链接，将自定义的仪表板共享为其测试驱动器，无需进一步的技术配置。 了解有关设置[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)模板应用的详细信息。

## <a name="deployment-subscription-details"></a>部署订阅详细信息

若要代表你部署测试驱动器，请创建并提供单独的唯一 Azure 订阅。 （对于 Power BI 测试驱动器不是必需的）。

- **Azure 订阅 id** （azure 资源管理器和逻辑应用需要）：输入订阅 id 可授予对 azure 帐户服务的访问权限，以供资源使用情况报告和计费。 如果还没有 Azure 订阅，请考虑[创建一个单独的 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)用于测试驱动器。 你可以通过登录到[Azure 门户](https://portal.azure.com/)并导航到左侧菜单的 "**订阅**" 选项卡来查找你的 Azure 订阅 ID。 选择该选项卡将显示您的订阅 ID （ `a83645ac-1234-5ab6-6789-1h234g764ghty`例如）。

- **Azure AD 租户 id** （必需）：输入 AZURE ACTIVE DIRECTORY （AD）[租户 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 "Active Directory" 选项卡，选择 "**属性**"，然后查找列出的**目录 ID**号（例如`50c464d3-4930-494c-963c-1e951d15360e`）。 你还可以使用域名 URL 查找你组织的租户 ID，网址为： [https://www.whatismytenantid.com](https://www.whatismytenantid.com)。

- **Azure AD 租户名称**（动态365需要）：输入 AZURE ACTIVE DIRECTORY （AD）名称。 若要查找此名称，请登录到[Azure 门户](https://portal.azure.com/)，在右上角，你的租户名称将在你的帐户名称下面列出。

- **Azure AD 应用 id** （必需）：输入 AZURE ACTIVE DIRECTORY （AD）[应用程序 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 "Active Directory" 选项卡，选择 "**应用注册**"，然后查找列出的**应用程序 ID**号（ `50c464d3-4930-494c-963c-1e951d15360e`例如）。

- **Azure AD 应用客户端机密**（必需）：输入 Azure AD 应用程序[客户端机密](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要查找此值，请登录到[Azure 门户](https://portal.azure.com/)。 选择左侧菜单中的 " **Azure Active Directory** " 选项卡，选择 "**应用注册**"，然后选择你的测试驱动器应用。 接下来，选择 "**证书和密码**"，选择 "**新建客户端密码**"，输入描述，选择 "**永不****过期**"，然后选择 "**添加**"。 请确保复制值。 （在执行此操作之前，请勿导航离开页面，否则将无法访问此值。）

请记住在转到下一节之前**保存**。

## <a name="test-drive-listings-optional"></a>测试驱动器列表（可选）

"**测试驱动器**" 选项卡下的 "**测试驱动器列表**" 选项显示你的测试驱动器可用的语言（和市场），当前只有英语（美国）是可用的位置。 此外，此页还显示特定于语言的列表的状态以及添加它的日期/时间。 需要为每种语言/市场定义测试驱动器详细信息（说明、用户手册、视频等）。

- **说明**（必需）：描述您的测试驱动器、要演示的内容、要浏览的功能，以及帮助用户确定是否获取您的产品/服务的相关信息。 最多可在此字段中输入3000个字符的文本。

- **访问信息**（对于 Azure 资源管理器和逻辑测试驱动器是必需的）：说明需要了解哪些客户才能访问和使用此试用版。 逐步完成使用产品/服务的方案，并确切了解客户在整个测试过程中访问功能应知道的情况。 最多可在此字段中输入10000个字符的文本。

- **用户手册**（必需）：深入演练测试驱动器体验。 用户手册应确切地说明您希望客户从遇到该测试驱动器中获得的内容，并为他们可能遇到的任何问题提供参考。 文件必须采用 PDF 格式，并在上传后命名为（最多255个字符）。

- **视频：添加视频**（可选）：可以将视频上传到 YouTube 或 Vimeo，并使用链接和缩略图（533 x 324 像素）在此处引用视频，以便客户可以查看信息，以帮助他们更好地了解测试驱动器，包括如何成功使用产品/服务的功能并了解突出显示其优势的方案。
  - **名称**（必需）
  - **URL （仅限 YouTube 或 Vimeo）** （必需）
  - **缩略图（533 x 324 px）** -图像文件必须采用 PNG 格式。

完成这些字段后，选择 "**保存**"。

## <a name="next-steps"></a>后续步骤

[在商业市场中更新现有的套餐](./update-existing-offer.md)
