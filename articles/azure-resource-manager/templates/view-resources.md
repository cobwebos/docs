---
title: 发现资源属性
description: 描述如何搜索资源属性。
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c8bbe0dcb1bc9dc9751a1dc0d0b98a6368473546
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327742"
---
# <a name="discover-resource-properties"></a>发现资源属性

创建资源管理器模板之前，需要了解有哪些资源类型可用以及要在模板中使用哪些值。 本文介绍了一些可用于查找要在模板中包含的属性的方法。

## <a name="find-resource-provider-namespaces"></a>查找资源提供程序命名空间

ARM 模板中的资源是使用资源提供程序命名空间和资源类型来定义的。 例如，Microsoft.Storage/storageAccounts 是存储帐户资源类型的完整名称。 Microsoft.Storage 是命名空间。 如果你还不知道要使用的资源类型的命名空间，请参阅 [Azure 服务的资源提供程序](../management/azure-services-resource-providers.md)。

![资源管理器资源提供程序命名空间映射](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>导出模板

为现有资源获取模板属性的最简单方法就是导出模板。 有关详细信息，请参阅[在 Azure 门户中将单个资源和多个资源导出到模板](./export-template-portal.md)。

## <a name="use-resource-manager-tools-extension"></a>使用资源管理器工具扩展

Visual Studio Code 和 Azure 资源管理器工具扩展有助于确切了解每种资源类型需要哪些属性。 它们提供 Intellisense 和代码片段，可简化在模板中定义资源的过程。 有关详细信息，请参阅[快速入门：使用 Visual Studio Code 创建 Azure 资源管理器模板](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource)。

以下屏幕截图显示了如何将存储帐户资源添加到模板：

![资源管理器工具扩展代码片段](./media/view-resources/resource-manager-tools-extension-snippets.png)

该扩展还提供了用于配置属性的选项列表。

![资源管理器工具扩展可配置值](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>使用模板参考

Azure 资源管理器模板引用是最全面的模板架构资源。 你可以找到 API 版本、模板格式和属性信息。

1. 浏览到 [Azure 资源管理器模板引用](/azure/templates/)。
1. 从左侧导航栏中，选择 " **存储**"，然后选择 " **所有资源**"。 "所有资源" 页汇总了资源类型和版本。

    ![模板引用资源版本](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    如果你知道资源类型，则可以直接跳到此页面，URL 格式如下： `https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}` 。

1. 选择最新版本。 建议使用最新的 API 版本。

    " **模板格式** " 部分列出了存储帐户的所有属性。 列表中列出了**sku** ：

    ![模板引用存储帐户格式](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    向下滚动以查看 "**属性值**" 部分中的 " **Sku 对象**"。 本文显示了 SKU 名称允许的值：

    ![模板引用存储帐户 SKU 值](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    在页面末尾， **快速入门模板** 部分列出了一些包含资源类型的 Azure 快速入门模板：

    ![模板引用存储帐户快速入门模板](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

模板参考与每个 Azure 服务文档站点关联。  例如， [Key Vault 文档站点](../../key-vault/general/overview.md)：

![资源管理器模板引用 Key Vault](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>使用资源浏览器

资源浏览器是嵌入在 Azure 门户中的。 在使用此方法之前，需要先有一个存储帐户。 如果还没有，请选择以下按钮来创建一个：

[![“部署到 Azure”](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在搜索框中，输入“资源浏览器”，然后选择“资源浏览器”。

    ![屏幕截图显示在 Azure 门户中搜索资源浏览器。](./media/view-resources/azure-portal-resource-explorer.png)

1. 从左侧展开“订阅”，然后展开你的 Azure 订阅。 在“提供程序”或“ResourceGroups”下可找到存储帐户。

    ![Azure 门户资源浏览器](./media/view-resources/azure-portal-resource-explorer-home.png)

    - 提供程序：展开“提供程序” -> “Microsoft.Storage” -> “storageAccounts”，然后选择你的存储帐户。
    - ResourceGroups：选择包含该存储帐户的资源组，选择“资源”，然后选择该存储帐户。

    在右侧，可以看到现有存储帐户的 SKU 配置，如下所示：

    ![Azure 门户资源浏览器存储帐户 sku](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>使用 Resources.azure.com

Resources.azure.com 是一个公共网站，可供具有 Azure 订阅的任何人员访问。 它现在是预览版。  请考虑改用“[资源浏览器](#use-resource-explorer)”。 此工具提供了这些功能：

- 发现 Azure 资源管理 API。
- 获取 API 文档和架构信息。
- 直接在自己的订阅中进行 API 调用。

若要演示如何通过使用此工具来检索架构信息，需要有一个存储帐户。 如果还没有，请选择以下按钮来创建一个：

[![“部署到 Azure”](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. 浏览到 [resources.azure.com](https://resources.azure.com/)。 该工具填充左侧窗格需要几分钟时间。
1. 选择“订阅”。

    ![resource.azure.com api 映射](./media/view-resources/resources-azure-com-api-mapping.png)

    左侧的节点与右侧的 API 调用匹配。 可通过选择“获取”按钮进行 API 调用。
1. 从左侧展开“订阅”，然后展开你的 Azure 订阅。 在“提供程序”或“ResourceGroups”下可找到存储帐户。

    - 提供程序：展开“提供程序” -> “Microsoft.Storage” -> “storageAccounts”，然后浏览到该存储帐户。
    - ResourceGroups：选择包含该存储帐户的资源组，然后选择“资源”。

    在右侧，可以看到现有存储帐户的 sku 配置，如下所示：

    ![Azure 门户资源浏览器存储帐户 sku](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>后续步骤

本文介绍了如何查找模板架构信息。 若要详细了解如何创建资源管理器模板，请参阅[了解 ARM 模板的结构和语法](./template-syntax.md)。
