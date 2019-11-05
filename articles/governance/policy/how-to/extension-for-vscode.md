---
title: 适用于 Visual Studio Code 的 Azure 策略扩展
description: 了解如何使用适用于 Visual Studio Code 的 Azure 策略扩展查找资源管理器别名。
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 20af29c40ec13add90294e28d64f0166acc95319
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514805"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>使用 Azure Policy extension 进行 Visual Studio Code

> 适用于 Azure 策略扩展版本**0.0.21**和更高版本

了解如何使用适用于 Visual Studio Code 的 Azure 策略扩展查找[别名](../concepts/definition-structure.md#aliases)以及查看资源和策略。 首先，我们将介绍如何在 Visual Studio Code 中安装 Azure 策略扩展。 接下来，我们将演练如何查找别名。

可在 Visual Studio Code 支持的所有平台上安装适用于 Visual Studio Code 的 Azure 策略扩展。 此支持包括 Windows、Linux 和 macOS。

> [!NOTE]
> 在 Visual Studio Code 的 Azure 策略扩展中查看的策略所做的更改不会同步到 Azure。

## <a name="prerequisites"></a>必备组件

完成本文中的步骤需要具有以下项：

- Azure 订阅。 如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
- [Visual Studio Code](https://code.visualstudio.com)。

## <a name="install-azure-policy-extension"></a>安装 Azure 策略扩展

满足先决条件后，可以按照以下步骤为 Visual Studio Code 安装 Azure 策略扩展：

1. 打开 Visual Studio Code。

1. 在菜单栏中，转到“视图” **“扩展”。**  > 

1. 在搜索框中，输入 " **Azure 策略**"。

1. 从搜索结果中选择 " **Azure 策略**"，然后选择 "**安装**"。

1. 根据需要选择“重载”。

## <a name="set-the-azure-environment"></a>设置 Azure 环境

对于国家云用户，请按照以下步骤设置 Azure 环境：

1. 选择“文件”>“首选项”>“设置”。

1. 搜索以下字符串： _Azure： Cloud_

1. 从列表中选择 "国家/地区" 云：

   ![为 Visual Studio Code 设置默认的 Azure 云登录](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>连接到 Azure 帐户

若要评估资源和查找别名，必须连接到 Azure 帐户。 请按照以下步骤从 Visual Studio Code 连接到 Azure：

1. 从 Azure 策略扩展或命令面板登录到 Azure。

   - Azure 策略扩展

     从 Azure 策略扩展中，选择 "**登录到 Azure**"。

     ![Azure 云登录，用于从 Azure 策略扩展 Visual Studio Code](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - 命令面板

     从菜单栏中转到 "**查看** > **命令面板**"，然后输入**Azure：登录**。

     ![用于从命令面板 Visual Studio Code 的 Azure 云登录](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. 按照登录说明登录到 Azure。 连接后，Visual Studio Code 窗口底部的状态栏上会显示 Azure 帐户名称。

## <a name="select-subscriptions"></a>选择订阅

首次登录时，Azure 策略扩展仅加载默认订阅资源和策略。 若要添加或删除订阅以显示资源和策略，请执行以下步骤：

1. 从命令面板或窗口脚注启动 "订阅" 命令。

   - 命令面板： 

     从菜单栏中转到 "**查看** > **命令面板**"，然后输入 " **Azure 策略：选择订阅**"。

   - 窗口页脚

     在屏幕底部的窗口页脚中，选择与**Azure： \<帐户\>** 相匹配的段。

1. 使用 "筛选器" 框可以按名称快速查找订阅。 然后，选中或删除每个订阅的检查，以设置 Azure 策略扩展显示的订阅。 添加或删除要显示的订阅后，请选择 **"确定"** 。

## <a name="search-for-and-view-resources"></a>搜索和查看资源

Azure 策略扩展在 "**资源**" 窗格中按资源提供程序和资源组列出所选订阅中的资源。 Treeview 包含所选订阅中或订阅级别的以下资源分组：

- **资源提供程序**
  - 每个已注册的资源提供程序，具有具有策略别名的资源和相关子资源
- **资源组**
  - 按资源组所在的资源组的所有资源

默认情况下，扩展会按具有策略别名的现有资源和资源筛选 "资源提供程序" 部分。 在**设置**中更改此行为 > **扩展** > **Azure 策略**，以查看所有资源提供程序而不进行筛选。

如果客户在单个订阅中有数百或数千个资源，则可能更倾向于查找其资源。 使用 Azure 策略扩展可以搜索特定的资源，步骤如下：

1. 从 Azure 策略扩展或命令面板启动搜索界面。

   - Azure 策略扩展

     在 Azure 策略扩展中，将鼠标悬停在 "**资源**" 面板上，选择省略号，然后选择 "**搜索资源**"。

   - 命令面板：

     从菜单栏中转到 "**查看**>**命令面板**"，然后输入**资源： "搜索资源**"。

1. 如果为显示选择了多个订阅，请使用筛选器来选择要搜索的订阅。

1. 使用筛选器选择要搜索的资源组，该资源组是以前选择的订阅的一部分。

1. 使用筛选器选择要显示的资源。 此筛选器适用于资源名称和资源类型。

## <a name="discover-aliases-for-resource-properties"></a>发现资源属性的别名

选择资源时，无论是通过搜索界面还是在 treeview 中选择资源，Azure 策略扩展都会打开表示该资源的 JSON 文件及其所有资源管理器属性值。

打开资源后，将鼠标悬停在资源管理器属性名称或值上会显示 Azure 策略别名（如果存在）。 在此示例中，资源是 `Microsoft.Compute/virtualMachines` 资源类型， **imageReference**属性正在悬停。 悬停显示匹配的别名。

![Azure 策略扩展悬停显示资源管理器属性别名](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>搜索并查看策略和分配

Azure 策略扩展会列出策略类型和策略分配，作为选择要在 "**策略**" 窗格中显示的订阅的 treeview。 如果客户在单个订阅中有数百或数千个策略或分配，则可以使用一种可搜索的方式来查找其策略或分配。 使用 Azure 策略扩展可以搜索特定的策略或分配，步骤如下：

1. 从 Azure 策略扩展或命令面板启动搜索界面。

   - Azure 策略扩展

     在 Azure 策略扩展中，将鼠标悬停在 "**策略**" 面板上，选择省略号，然后选择 "**搜索策略**"。

   - 命令面板：

     从菜单栏中转到 "**查看**>**命令面板**"，然后输入 "**策略：搜索策略**"。

1. 如果为显示选择了多个订阅，请使用筛选器来选择要搜索的订阅。

1. 使用筛选器可以选择作为以前所选订阅的一部分的搜索的策略类型或分配。

1. 使用筛选器可以选择要显示的策略。 此筛选器适用于策略定义或策略分配的_displayName_ 。

选择策略或分配时，无论是通过搜索界面还是在 treeview 中选择策略或分配，Azure 策略扩展都将打开表示策略或分配及其所有资源管理器属性值的 JSON。 此扩展可以验证已打开的 Azure 策略 JSON 架构。

## <a name="sign-out"></a>注销

从菜单栏中转到 "**查看** > **命令面板**"，然后输入 " **Azure：注销**"。

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[修正不符合的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。