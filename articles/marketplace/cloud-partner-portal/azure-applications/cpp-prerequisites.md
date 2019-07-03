---
title: Azure 应用程序产品/服务先决条件 |Azure Marketplace
description: 在 Azure 市场上发布 Azure 应用程序产品/服务的先决条件。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 64039234a3863332ca19b915fb59a5271625d695
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258181"
---
# <a name="azure-application-prerequisites"></a>Azure 应用程序先决条件

本文介绍了在 Azure 市场上发布托管应用程序产品/服务的技术和业务先决条件。  如果尚未这样做，请查看以下信息源：
- 根据你的 SKU 类型，或者[Azure 应用程序：解决方案模板产品/服务发布指南](../../marketplace-solution-templates.md)或[Azure 应用程序：托管应用程序产品/服务发布指南](../../marketplace-managed-apps.md)
- [为 Azure Marketplace 中生成解决方案模板和托管应用程序](https://channel9.msdn.com/Events/Build/2018/BRK3603)视频


## <a name="technical-requirements"></a>技术要求

技术要求包括以下项：

*   Azure 资源管理器模板 有关详细信息，请参阅[了解 Azure 资源管理器模板的结构和语法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。 本文介绍 Azure 资源管理器模板的结构。 演示了模板的不同部分，以及可在相应部分使用的属性。 模板中包含可用于为部署构造值的 JSON 和表达式。 
* Azure 快速入门模板。<br> 有关详细信息，请参阅：

  * [Azure 快速入门模板](https://azure.microsoft.com/documentation/templates/)。 使用社区提供的模板通过 Azure 资源管理器部署 Azure 资源以执行更多操作。 Azure 资源管理器可让你使用声明性模板预配应用程序。 在单个模板中，你可以部署多个服务及其依赖项。 在应用程序生命周期的每个阶段，可使用相同模板重复部署应用程序。
  * [GitHub：Azure 资源管理器快速入门模板](https://github.com/azure/azure-quickstart-templates)。 此存储库包含由社区提供的所有当前可用的 Azure 资源管理器模板。 [https://azure.microsoft.com/documentation/templates/](https://azure.microsoft.com/documentation/templates/ ) 中维护了一个可搜索的模板索引。
* 创建 UI 定义<br>
有关详细信息，请参阅[为托管应用程序创建 Azure 门户用户界面](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)。 本文介绍了 createUiDefinition.json 文件的核心概念。 Azure 门户使用此文件生成用于创建托管应用程序的用户界面。


## <a name="business-requirements"></a>业务要求

业务需求包括在规程、合同和法律方面的以下义务：

* 你必须是已注册的云市场发布者。 如果你要注册，请按照文章中的步骤[成为云 Marketplace 发布者](https://docs.microsoft.com/azure/marketplace/become-publisher
)。

>[!NOTE]
>应当使用相同的 Microsoft Developer Center 注册帐户登录到云合作伙伴门户。 对于 Azure 市场产品/服务，只应具有一个 Microsoft 帐户。 此帐户不应特定于个别服务或产品/服务。

* 你的公司 （或其子公司） 必须是销售-从-国家/地区中支持的 Azure Marketplace。 这些国家/地区的当前列表，请参阅[Microsoft Azure Marketplace 参与政策](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)。
* 产品必须以兼容 Azure 市场支持的计费模型的方式获得许可。 有关详细信息，请参阅 Azure 市场中的[计费选项](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations)。
* 你需要以商业上合理的方式负责向客户提供技术支持。 支持可以采用免费、付费或社区支持等多种方式。
* 负责获得自己的软件和任何第三方软件依赖项的许可。
* 提供的内容必须满足在 Azure 市场上和 Azure 门户中列入名单的产品/服务的标准。
* 必须同意 Microsoft Azure 市场参与政策与发布者协议的条款。
* 你必须同意遵守 Microsoft Azure 网站使用条款、Microsoft 隐私声明和 Microsoft Azure 认证计划协议。


## <a name="publishing-requirements"></a>发布要求

若要发布新的 Azure 应用程序产品/服务，必须满足以下先决条件：

* 使元数据可供使用。 下面的列表（非详尽）显示了此元数据的示例：
  * 标题
  * 说明（采用 HTML 格式）
  * 一个徽标图像（采用 PNG 格式），采用以下固定图像大小：40 x 40 像素、90 x 90 像素、115 x 115 像素和 255 x 115 像素。
* 一个*Terms of Use*和一个*隐私策略*文档
* 应用程序文档
* 支持联系人


## <a name="next-steps"></a>后续步骤

满足所有需求后，即可开始[创建 Azure 应用程序产品/服务](./cpp-create-offer.md)。 
 
