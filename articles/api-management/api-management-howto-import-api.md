---
title: "API 管理关键概念"
description: "了解有关 API、产品、角色、组和其他 API 管理关键概念。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 40398b0a-ac2c-43f0-89e1-07e4abbf502f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 126fd84259c604785cdd5db0543f78539c1db9c7


---
# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>如何通过 Azure API 管理中的操作导入 API 的定义
在 API 管理中，可以创建新的 API，并手动添加操作，或者 API 可以在单个步骤中与操作一起导入。

API 和它们的操作可以使用以下格式导入。

* WADL
* Swagger

本指南演示如何创建一个新的 API，并在一个步骤中导入它。 有关手动创建一个 API 和添加操作的信息，请参阅[如何创建 API][如何创建 API] 和[如何将操作添加到 API][如何将操作添加到 API]。

## <a name="import-api"> </a>导入 API
API 在发布者门户中创建并发布。 若要访问发布者门户，请在 API 管理服务的 Azure 门户中单击“发布者门户”。 如果尚未创建 API 管理服务实例，请参阅 [创建 API 管理服务实例][创建 API 管理服务实例]教程中的[创建 API 管理服务实例][创建 API 管理服务实例]。

![发布者门户][api-management-management-console]

单击左侧“API 管理”菜单中的“API”，然后单击“导入 API”。

![导入 API][api-management-import-apis]

“导入 API”窗口具有对应于提供 API 规范的三种方法的三个选项卡。

* “从剪贴板”允许将 API 规格粘贴到指定文本框。
* “从文件”允许浏览并选择包含 API 规范的文件。
* “从 URL”允许向 API 的规范提供 URL。

![导入 API 格式][api-management-import-api-clipboard]

提供 API 规范后，使用右侧的单选按钮来指示的规范格式。 支持以下格式任务：

* WADL
* Swagger

接下来，输入 **Web API URL 后缀**。 这附加到您的 API 管理服务的基础 URL。 基础 URL 对 API 管理服务的每个实例上托管的所有 API 很常见。 API 管理通过后缀区分 API，并且对于特定的 API 管理服务实例特定的每个 API，此后缀必须唯一。

所有值都输入后，单击“保存”创建 API 和关联的操作。 

> [!NOTE]
> 有关以 Swagger 格式导入 Basic Calculator API 的教程，请参阅[在 Azure API 管理中管理第一个 API](api-management-get-started.md)。
> 
> 

## <a name="export-api"> </a> 导出 API
除了导入新的 API，可以从发布者门户导出 API 的定义。 若要这样做，请单击“API”的“摘要”选项卡中的“导出 API”。

![导出 API][api-management-export-api]

可以使用 WADL 或 Swagger 导出 API。 选择所需的格式，请单击“保存”，然后选择要在其中保存该文件的位置。

![导出 API 格式][api-management-export-api-format]

## <a name="next-steps"> </a>后续步骤
API 创建并导入操作后，您可以查看和配置任何其他设置，将 API 添加到一种产品并将其发布，这样就可提供给开发人员。 有关详细信息，请参阅下面的指南。

* [如何配置 API 设置][如何配置 API 设置]
* [如何创建和发布产品][如何创建和发布产品]

[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[导入 API]: #import-api
[导出 API]: #export-api
[配置 API 设置]: #configure-api-settings
[后续步骤]: #next-steps

[创建 API 管理服务实例]: api-management-get-started.md
[创建 API 管理服务实例]: api-management-get-started.md#create-service-instance

[如何将操作添加到 API]: api-management-howto-add-operations.md
[如何创建和发布产品]: api-management-howto-add-products.md
[如何创建 API]: api-management-howto-create-apis.md
[如何配置 API 设置]: api-management-howto-create-apis.md#configure-api-settings



<!--HONumber=Nov16_HO3-->


