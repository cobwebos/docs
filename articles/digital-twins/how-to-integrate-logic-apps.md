---
title: 与逻辑应用集成
titleSuffix: Azure Digital Twins
description: 请参阅如何使用自定义连接器将逻辑应用连接到 Azure 数字孪生
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: baf89ec75f844ae1a1f7797d26d2fb04a0d5df34
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849837"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>使用自定义连接器与逻辑应用集成

[Azure 逻辑应用](../logic-apps/logic-apps-overview.md) 是一种云服务，可帮助你跨应用和服务自动执行工作流。 通过将逻辑应用连接到 Azure 数字孪生 Api，你可以围绕 Azure 数字孪生及其数据创建此类自动流程。

Azure 数字孪生当前没有针对逻辑应用的已认证 (预建) 连接器。 相反，将逻辑应用与 Azure 数字孪生配合使用的当前过程是使用已修改为可用于逻辑应用的[自定义 Azure 数字孪生 Swagger](https://docs.microsoft.com/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)创建[**自定义逻辑应用连接器**](../logic-apps/custom-connector-overview.md)。

> [!NOTE]
> 上面链接的自定义 Swagger 示例包含多个版本的 Swagger。 最新版本将在具有最近日期的子文件夹中找到，但仍支持示例中包含的较早版本。

本文介绍如何使用[Azure 门户](https://portal.azure.com)**创建自定义连接器**，此连接器可用于将逻辑应用连接到 Azure 数字孪生实例。 然后，将 **创建一个逻辑应用** ，该应用使用此连接作为示例方案，在此方案中，计时器触发的事件会自动更新 Azure 数字孪生实例中的非整数。 

## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前**创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** 。
利用此帐户登录到 [Azure 门户](https://portal.azure.com) 。 

还需要在安装先决条件过程中完成以下各项。 本部分的其余部分将指导你完成以下步骤：
- 设置 Azure 数字孪生实例
- 获取应用注册客户端密钥
- 添加数字输出

### <a name="set-up-azure-digital-twins-instance"></a>设置 Azure 数字孪生实例

若要将 Azure 数字孪生实例连接到本文中的逻辑应用，需要已设置 **Azure 数字孪生实例** 。 

首先，设置 Azure 数字孪生实例及所需的身份验证，以便能够使用它。 为此，请按照[如何：*设置实例和身份验证*](how-to-set-up-instance-portal.md)中的说明设置实例和身份验证。 根据你的首选体验，针对 [Azure 门户](how-to-set-up-instance-portal.md)、[CLI](how-to-set-up-instance-cli.md) 或[自动化 Cloud Shell 部署脚本示例](how-to-set-up-instance-scripted.md)提供了有关设置的文章。 所有版本的说明还包含用于验证是否已成功完成每个步骤并准备好继续使用新实例的步骤。

在本教程中，你将在设置实例时需要多个值。 如果需要再次收集这些值，请使用下面的链接转至设置文章中的相应部分，以在 [Azure 门户](https://portal.azure.com)中找到这些值。
* Azure 数字孪生实例主机名（[在门户中查找](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)）
* Azure AD 应用注册应用程序（客户端）ID（[在门户中查找](how-to-set-up-instance-portal.md#collect-important-values)）
* Azure AD 应用注册目录（租户）ID（[在门户中查找](how-to-set-up-instance-portal.md#collect-important-values)）

### <a name="get-app-registration-client-secret"></a>获取应用注册客户端密钥

还需要创建 Azure AD 应用注册的 **_客户端机密_** 。 为此，请导航到 Azure 门户中的 " [应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) " 页 (你可以使用此链接，或在门户搜索栏) 中查找它。 从列表中选择注册，以打开其详细信息。 

从注册的菜单中点击 *证书和机密* ，然后选择 " *+ 新建客户端密钥*"。

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " *添加*"。

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " 图标将其复制到剪贴板) 

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 ":::

### <a name="add-a-digital-twin"></a>添加数字输出

本文使用逻辑应用来更新 Azure 数字孪生实例中的克隆。 若要继续，你应在实例中至少添加一个 "双子入"。 

可以使用 [DigitalTwins api](how-to-use-apis-sdks.md)、 [.Net (c # ) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或 [Azure 数字孪生 CLI](how-to-use-cli.md)添加孪生。 有关如何使用这些方法创建孪生的详细步骤，请参阅 [*操作方法：管理数字孪生*](how-to-manage-twin.md)。

你将需要已创建的实例中的克隆的克隆 **_ID_** 。

## <a name="create-custom-logic-apps-connector"></a>创建自定义逻辑应用连接器

在此步骤中，将创建 Azure 数字孪生 Api 的 [自定义逻辑应用连接器](../logic-apps/custom-connector-overview.md) 。 完成此操作后，在下一部分中创建逻辑应用时，可以将 Azure 数字孪生挂钩。

导航到 Azure 门户中的 " [逻辑应用自定义连接器](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) " 页面 (你可以使用此链接，或在门户搜索栏) 中搜索它。 单击 " *+ 添加*"。

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " 页面中，选择你的订阅和资源组，以及新连接器的名称和部署位置。 点击 *评审 + 创建*。 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " 选项卡，你可以在 *底部点击 "创建"* 来创建资源。

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " 按钮，在门户中查看连接器的详细信息。

### <a name="configure-connector-for-azure-digital-twins"></a>为 Azure 数字孪生配置连接器

接下来，将创建的连接器配置为连接到 Azure 数字孪生。

首先，下载已修改为可用于逻辑应用的自定义 Azure 数字孪生 Swagger。 通过点击 "*下载 ZIP* " 按钮，从此[**链接**](https://docs.microsoft.com/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)下载**Azure 数字孪生自定义 swagger**示例。 导航到下载的 *Azure_Digital_Twins_Custom_Swaggers.zip* 文件夹并将其解压缩。 

本教程的自定义 Swagger 位于 _**Azure_Digital_Twins_Custom_Swaggers \logicapps**_ "文件夹中。 此文件夹包含名为 " *稳定* " 和 " *预览*" 的子文件夹，这两者都包含按日期组织的不同版本的 Swagger。 具有最近日期的文件夹将包含 Swagger 的最新副本。 无论选择哪种版本，Swagger 文件都命名为 _**digitaltwins.js**_。

> [!NOTE]
> 除非使用的是预览功能，否则通常建议使用最新的 Swagger *稳定* 版本。 不过，还会支持更早版本的 Swagger 和预览版本。 

接下来，请在 [Azure 门户](https://portal.azure.com) 中中转到连接器的 "概述" 页，然后单击 " *编辑*"。

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " 按钮，继续执行下一个配置步骤。

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " 以生成重定向 URL*。 现在，通过在窗格顶部命中 *更新连接器* 来确认连接器设置。

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " 字段并复制已生成的值。 将在下一步中使用它。

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " 将重新启动输入配置选择的整个过程。 它不会从上次操作时填充值，因此，如果要使用任何更改的值保存更新的配置，则必须重新输入其他所有值，以避免被默认值覆盖。

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>在 Azure AD 应用程序中授予连接器权限

接下来，使用上一步骤中复制的自定义连接器的 " *重定向 URL* " 值在 Azure AD 应用注册中授予连接器权限。

导航到 Azure 门户中的 " [应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) " 页，然后从列表中选择你的注册。 

在注册的菜单中的 " *身份验证* " 下，添加 URI。

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " 图标。

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 ":::

你现在已经设置了一个可访问 Azure 数字孪生 Api 的自定义连接器。 

## <a name="create-logic-app"></a>创建逻辑应用

接下来，你将创建一个逻辑应用，该应用将使用新连接器自动执行 Azure 数字孪生更新。

在 [Azure 门户](https://portal.azure.com)的门户搜索栏中搜索 " *逻辑应用* "。 选择它应转到 " *逻辑应用* " 页。 点击 " *创建逻辑应用* " 按钮以创建新的逻辑应用。

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " 按钮以继续访问 *逻辑应用设计器*，你将在该设计器中填充工作流的逻辑。

### <a name="design-workflow"></a>设计工作流

在 *逻辑应用设计器*中，在 " *使用常见触发器启动*" 下，选择 " _**重复周期**_"。

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " 选项卡。你应会在顶部的框中看到自定义连接器。

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 " _+ 新建步骤_ "。

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;&quot;:::

输入想要用于说明和过期的任何值，然后单击 ":::

## <a name="query-twin-to-see-the-update"></a>查询克隆以查看更新

既然已经创建了逻辑应用，则在逻辑应用设计器中定义的克隆更新事件应该每三秒钟重复一次。 这意味着，三秒钟后，您应该能够查询您的克隆，并看到新修补后的值反映。

你可以通过 (选择的方法（如 [自定义客户端应用](tutorial-command-line-app.md)、 [Azure 数字孪生资源管理器示例应用](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)、 [sdk 和 api](how-to-use-apis-sdks.md)或 [CLI](how-to-use-cli.md)) ）查询你的。 

有关查询 Azure 数字孪生实例的详细信息，请参阅 [*如何：查询双子图形*](how-to-query-graph.md)。

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建一个逻辑应用，该应用会定期用提供的修补程序来更新 Azure 数字孪生实例中的克隆。 可以尝试在自定义连接器中选择其他 Api，为实例上的各种操作创建逻辑应用。

若要详细了解可用的 Api 操作和所需的详细信息，请访问 [*操作方法：使用 Azure 数字孪生 api 和 sdk*](how-to-use-apis-sdks.md)。