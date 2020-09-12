---
title: 将实例移动到不同的 Azure 区域
titleSuffix: Azure Digital Twins
description: 请参阅如何将 Azure 数字孪生实例从一个 Azure 区域移到另一个。
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e2cb8ee282666d7a9a567ca04762b26de3b3b9bd
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89443035"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>将 Azure 数字孪生实例移动到不同的 Azure 区域

如果需要将 Azure 数字孪生实例从一个区域移到另一个区域，当前过程是 **在新区域中重新创建资源**，然后删除原始资源。 在此过程结束时，你将使用与第一个相同的新 Azure 数字孪生实例，更新的位置除外。

本文提供了有关如何执行完整移动的指导，复制所有需要使新实例与原始实例匹配的内容。

此过程包括下列步骤：
1. 准备：下载原始模型、孪生和图形。
2. 移动：在新区域中创建新的 Azure 数字孪生实例。
3. 移动：重新填充新的 Azure 数字孪生实例。
    - 上载原始模型、孪生和图形。
    - 重新创建终结点和路由。
    - 重新链接连接的资源。
4. 清理源资源：删除原始实例。

## <a name="prerequisites"></a>先决条件

在尝试重新创建 Azure 数字孪生实例之前，最好遍历原始实例的组件，并清楚地了解需要重新创建的所有部分。

下面是一些你可能想要考虑的问题：
* 上载到我的实例的 **模型** 是什么？ 有多少？
* 我的实例中有哪些 **孪生** ？ 有多少？
* **图形**在我的实例中的常规形状是什么？ 有多少关系？
* 我的实例中有哪些 **终结点** ？
* 我的实例中有哪些 **路由** ？ 它们是否具有筛选器？
* 我的实例在何处 **连接到其他 Azure 服务**？ 一些常见集成点包括 .。。
    - 事件网格、事件中心或服务总线
    - Azure Functions
    - 逻辑应用
    - 时序见解
    - Azure Maps
    - 设备预配服务 (DPS) 
* 连接到我的实例还有哪些其他 **个人或公司应用** ？

可以使用 [Azure 门户](https://portal.azure.com)、 [Azure 数字孪生 api 和 Sdk](how-to-use-apis-sdks.md)、 [azure 数字孪生 CLI 命令](how-to-use-cli.md)或 [Azure 数字孪生 (ADT) 资源管理器](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 示例来收集此信息。

## <a name="prepare"></a>准备

在本部分中，你将准备通过从原始实例 **下载原始模型、孪生和图形** 来重新创建实例。 本文介绍如何使用 [Azure 数字孪生 (ADT) 资源管理器](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 示例。

>[!NOTE]
>您可能已在实例中有包含模型和/或关系图的文件。 如果是这样，则您无需再次下载所有内容，只需下载这些文件后可能已更改的部分， (例如，可能已使用新数据) 进行更新的孪生。

### <a name="limitations-of-adt-explorer"></a>ADT 资源管理器的限制

[Azure 数字孪生 (ADT) 资源管理器示例](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)是一个客户端应用示例，支持图形的可视化表示形式，并提供与实例的可视化交互。 本文介绍如何使用它来下载，并在以后重新上传模型、孪生和图形。

但请注意，这是一个 **示例** ，而不是一个完整的工具。 尚未对其进行压力测试，且未构建来处理大小较大的图形。 因此，请记住以下现成的示例限制：
* 该示例当前仅在图形大小上经过测试，最多可达1000节点和2000关系
* 在出现任何间歇性故障时，此示例不支持重试。
* 如果上传的数据不完整，则该示例不一定通知用户
* 该示例不处理由于非常大的图形超出内存等可用资源而导致的错误

如果该示例无法处理图形的大小，可以使用其他 Azure 数字孪生开发人员工具导出和导入图形：
* [Azure 数字孪生 CLI 命令](how-to-use-cli.md)
* [Azure 数字孪生 Api 和 Sdk](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>设置 ADT 资源管理器应用程序

若要继续 ADT 资源管理器，请先下载示例应用程序代码，并将其设置为在计算机上运行。 

导航到此处的示例： [Azure 数字孪生 (ADT) 资源管理器](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 点击“下载 ZIP”按钮，将此示例代码的 .ZIP 文件作为 ADT_Explorer.zip 下载到计算机 __。 解压缩文件。

接下来，设置要在计算机上运行的 ADT Explorer 的权限。 为此，请按照 Azure 数字孪生快速入门中的 [*SET ADT 资源管理器权限*](quickstart-adt-explorer.md#set-adt-explorer-permissions) 部分中的步骤进行操作。

最后，运行并配置 ADT 资源管理器以连接到原始 Azure 数字孪生实例。 按照快速入门的 " [*运行和配置 ADT 资源管理器*](quickstart-adt-explorer.md#run-and-configure-adt-explorer) " 部分中的步骤进行操作。

现在，你应该在计算机上的浏览器中运行 ADT 资源管理器示例应用。 该示例应连接到原始 Azure 数字孪生实例。

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="显示在 localhost:3000 运行的应用的浏览器窗口。此应用名为 ADT 资源管理器，其中包含查询资源管理器、模型视图、图形视图和属性资源管理器所对应的框。屏幕上尚无数据。" lightbox="media/how-to-move-regions/explorer-blank.png":::

若要验证连接，您可以单击 " *运行查询* " 按钮以运行默认查询，该查询在图形 *资源管理器* 框中的关系图中显示所有孪生和关系。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="窗口顶部附近显示“运行查询”的按钮突出显示" lightbox="media/how-to-move-regions/run-query.png":::

你可以让 ADT 资源管理器运行，因为稍后将在本文中再次使用它将这些项重新上传到目标区域中的新实例。

### <a name="download-models-twins-and-graph"></a>下载模型、孪生和图形

接下来，将解决方案中的模型、孪生和 graph 下载到计算机。

要一次下载所有这些内容，首先请确保完整的图形显示在 *图形视图* 框中 (您可以通过 `SELECT * FROM digitaltwins` 在 " *查询资源管理器* ") 框中重新运行的默认查询来执行此操作。
 
然后，按 "*图形视图*" 框中的 "*导出关系图*" 图标。

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="在 "关系图视图" 框中，将突出显示图标。它会显示一个指向云的下拉箭头。" lightbox="media/how-to-move-regions/export-graph.png":::

这将在*图形视图*中启用 "*下载*" 链接。 选择此项可下载基于 JSON 的查询结果表示形式，包括模型、孪生和关系。 这应该 *将一个文件中的* 文件下载到你的计算机。

>[!NOTE]
>如果下载的文件似乎具有不同的文件扩展名，请尝试直接编辑扩展并将其更改为 *json*。

## <a name="move"></a>移动

接下来，通过在目标区域中创建一个新实例，并使用原始实例中的数据和组件填充该实例来完成 "移动" 实例。

### <a name="create-a-new-instance"></a>创建新实例

首先， **在目标区域中创建 Azure 数字孪生的新实例**。 为此，请执行 [*操作方法：设置实例和身份验证*](how-to-set-up-instance-scripted.md)中的步骤，记住以下这些指针：
* **如果**新实例位于不同的资源组中，则可以为其保留相同的名称。 如果需要使用包含原始实例的相同资源组，则新实例将需要其自己的不同名称。
* 当系统提示输入位置时，输入目标新区域。
* 无 **需** 重新创建应用注册。 新实例可以重复使用已有的应用注册。
    - 如果使用 [脚本](how-to-set-up-instance-scripted.md) 化安装文章，则可以在出现提示时，重新输入现有应用注册的详细信息，而不是输入新名称。
    - 如果使用手动 [门户](how-to-set-up-instance-portal.md) 或 [CLI](how-to-set-up-instance-cli.md) 安装程序项目，可以在 *创建 Azure 数字孪生实例* 并 *设置用户访问权限* 步骤之后停止。 无需继续通过 *设置客户端应用程序的访问权限*。

完成此操作后，你将需要新实例的 **主机名** ，以便继续对你的数据进行设置。 如果在安装过程中未记下此操作，则可以按照 [以下说明](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) 立即从 Azure 门户获取它。

### <a name="repopulate-old-instance"></a>重新填充旧实例

接下来，将设置新的实例，使其成为原始实例的副本。

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>使用 ADT 资源管理器上传原始模型、孪生和图形

在本部分中，您可以将模型、孪生和 graph 重新上传到新实例。 如果原始实例中没有任何模型、孪生或图形，或者不想将它们移到新实例，则可以跳到 [下一部分](#recreate-endpoints-and-routes)。

否则，若要继续，请返回运行 **ADT 资源管理** 器的浏览器窗口，并执行以下步骤。

##### <a name="connect-to-the-new-instance"></a>连接到新实例

目前，ADT 资源管理器已连接到原始 Azure 数字孪生实例。 通过点击窗口顶部的 " *登录* " 按钮，将连接切换为指向新的实例。 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="在窗口顶部附近突出显示“登录”图标的 ADT 资源管理器。图标显示了一个简单的人员与密钥叠加的剪影。" lightbox="media/how-to-move-regions/sign-in.png":::

由于你要重用应用程序注册，因此只需替换 *ADT URL*。 请将此值更改为它读取 https：/ */{new 实例主机名}*。

单击 " *连接*"。 系统可能会要求你再次登录 Azure 凭据，并/或向此应用程序授予对你的实例的许可。

##### <a name="upload-models-twins-and-graph"></a>上载模型、孪生和图形

接下来，将之前下载的解决方案组件上传到新实例。

若要上载**模型、孪生和 graph**，请点击 "*图形视图*" 框中的 "*导入关系图*" 图标。 此选项将立即上传这三个组件， (甚至在图形) 中当前未使用的模型。

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="在“图形视图”框中，一个图标突出显示。它显示了一个指向云的箭头。" lightbox="media/how-to-move-regions/import-graph.png":::

在 "文件选择器" 框中，导航到下载的关系图。 选择 *"graph"* 文件并单击 " *打开*"。

几秒钟后，ADT 资源管理器将打开“导入”视图，其中显示了要加载的图的预览。

若要确认图上传，请点击“图形视图”右上角的“保存”图标 ：

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="在“图形预览”窗格中突出显示“保存”图标" lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT 资源管理器现在会将模型和图形 () 添加到新的 Azure 数字孪生实例，包括 "孪生" 和 "关系"。 应该会看到一条成功消息，指出已上传了多少模型、孪生和关系：

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="指示关系图导入成功的对话框。它读取 "导入成功"。已导入2个模型。4孪生已导入。已导入2个关系。 "" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

若要验证是否已成功上传所有内容，请点击 "*图形资源管理器*" 框中的 "*运行查询*" 按钮，以运行在图形中显示所有孪生和关系的默认查询。 这也会在 *模型视图*中刷新模型列表。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="在窗口顶部附近的 "运行查询" 按钮周围突出显示" lightbox="media/how-to-move-regions/run-query.png":::

应该会看到图形，其中显示了 " *图形资源管理器* " 框中显示的所有孪生和关系。 您还应在 " *模型视图* " 框中看到您的模型。

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="ADT 资源管理器的视图，显示在 "模型视图" 框中突出显示的2个模型，在 "Graph 资源管理器" 框中突出显示了图形" lightbox="media/how-to-move-regions/post-upload.png":::

这会确认已将模型、孪生和 graph 重新上传到目标区域中的新实例。

#### <a name="recreate-endpoints-and-routes"></a>重新创建终结点和路由

如果原始实例中有 **终结点和/或路由** ，则需要在新实例中重新创建它们。 如果原始实例中没有任何终结点或路由，或者不想将它们移到新的实例中，则可以跳到 [下一部分](#re-link-connected-resources)。

否则，请继续执行 [*操作方法：使用新实例管理终结点和路由*](how-to-manage-routes-portal.md) 中的步骤，记住以下这些指针： 
* **不需要**重新创建要用于终结点的事件网格、事件中心或服务总线资源， (终结点说明) 中的*先决条件*部分。 你只需在 Azure 数字孪生实例上重新创建终结点。
* 你可以重复使用终结点和路由 **名称**，因为它们的作用域限定为不同的实例。
* 请记住，将任何所需的 **筛选器** 添加到所创建的路由。

#### <a name="re-link-connected-resources"></a>重新链接连接的资源

如果有其他应用或 Azure 资源连接到原始 Azure 数字孪生实例，则需要编辑连接，使其转到新实例。 这可能包括其他 Azure 服务、已设置为使用 Azure 数字孪生的个人或公司应用。

如果没有任何其他资源连接到原始实例，或者不想将它们移到新的实例，则可以跳到 [下一部分](#verify)。

否则，若要继续，请考虑方案中的连接资源。 无需删除并重新创建任何连接的资源;相反，你只需编辑通过其 **主机名**连接到 Azure 数字孪生实例的点，并将其更新为使用新实例的主机名而不是原始实例的主机名。

你需要编辑的确切资源取决于你的方案，但以下是一些常见的集成点：
* Azure Functions。 如果 Azure 函数的代码包含原始实例的主机名，则应将此值更新为新实例的主机名，然后重新发布函数。
* 事件网格、事件中心或服务总线
* 逻辑应用
* 时序见解
* Azure Maps
* 设备预配服务 (DPS) 
* Azure 之外的个人或公司应用，例如在教程中创建的 **客户端应用** [*：编写*](tutorial-code.md)连接到实例并调用 Azure 数字孪生 api 的客户端应用

完成此步骤后，目标区域中的新实例应为原始实例的副本。

## <a name="verify"></a>验证

若要验证是否已正确设置了新的实例，可以使用以下工具：
* [**Azure 门户**](https://portal.azure.com) (好于验证新实例是否存在并且位于正确的目标区域中;还适用于验证终结点和路由，以及与其他 Azure 服务的连接) 
* [Azure 数字孪生**CLI 命令**](how-to-use-cli.md) (好用于验证新实例是否存在并且位于正确的目标区域中;还可用于验证实例数据) 
* [**ADT Explorer**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (用于验证实例数据（如模型、孪生和图形）) 
* [Azure 数字孪生 api 和 sdk](how-to-use-apis-sdks.md) (用于验证实例数据，如模型、孪生和 graph;还适用于验证终结点和路由) 

你还可以尝试运行你的原始实例运行的任何自定义应用或端到端流，以帮助你验证它们是否正在正确使用新实例。

## <a name="clean-up-source-resources"></a>清理源资源

现在，你的新实例已在目标区域中使用原始实例的数据和连接的副本进行了设置，你可以 **删除原始实例**。

可在 [Azure 门户](https://portal.azure.com)、 [CLI](how-to-use-cli.md)或 [控制平面 api](how-to-use-apis-sdks.md#overview-control-plane-apis)中执行此操作。

若要使用 Azure 门户删除实例，请在浏览器窗口中 [打开门户](https://portal.azure.com) ，并通过在门户搜索栏中搜索其名称，导航到原始 Azure 数字孪生实例。

点击 " *删除* " 按钮，并按照提示完成删除操作。

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Azure 门户的 "概述" 选项卡上的 Azure 数字孪生实例详细信息的视图。突出显示 "删除" 按钮":::