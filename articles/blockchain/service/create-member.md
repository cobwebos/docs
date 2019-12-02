---
title: 创建 Azure 区块链服务成员 - Azure 门户
description: 使用 Azure 门户创建区块链联盟的 Azure 区块链服务成员。
ms.date: 11/18/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 90f859e4d2e2621afad9cab11d66e81018bd3147
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455785"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建 Azure 区块链服务区块链成员

本快速入门介绍如何使用 Azure 门户在 Azure 区块链服务中部署新的区块链成员和联盟。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>创建区块链成员

创建在新的或现有的联盟中运行仲裁账本协议的区块链成员。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户的左上角选择“创建资源”。 
1. 选择“区块链” > “Azure 区块链服务(预览版)”   。

    ![创建服务](./media/create-member/create-member.png)

    设置 | 说明
    --------|------------
    Subscription | 选择要用于你的服务的 Azure 订阅。 如果有多个订阅，请选择要计费的资源所在的订阅。
    Resource group | 可以创建新的资源组名称，也可以选择订阅中现有的资源组名称。
    区域 | 选择要创建的成员的所属区域。 联盟中所有成员的位置必须相同。
    协议 | Azure 区块链服务预览版目前支持仲裁协议。
    联盟 | 对于新联盟，请输入唯一的名称。 如果通过某个计划加入联盟，请选择要加入的联盟。
    Name | 为该 Azure 区块链服务成员选择一个唯一名称。 区块链成员名称只能包含小写字母和数字。 第一个字符必须是字母。 值长度必须介于 2 到 20 个字符之间。
    成员帐户密码 | 成员帐户密码用于加密为你的成员创建的 Ethereum 帐户的私钥。 你使用成员帐户和成员帐户密码进行联盟管理。
    说明 | 联盟的说明。
    定价 | 新服务的节点配置和成本。 选择“更改”链接，在“标准”层和“基本”层之间进行选择    。
    节点密码 | 成员的默认事务节点的密码。 连接到区块链成员的默认事务节点公共终结点时，请使用密码进行基本身份验证。

1. 选择“查看 + 创建”以验证自己的设置  。 选择“创建”  来预配服务。 预配大约需要 10 分钟。
1. 若要监视部署过程，请在工具栏上选择“通知”。 
1. 在部署后，导航到你的区块链成员。

选择“概述”  ，你可以查看你的服务的基本信息，包括 RootContract 地址和成员帐户。

![区块链成员概述](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>清理资源

你可以将所创建的成员用于下一快速入门或教程。 不再需要这些资源时，可以通过删除为该快速入门创建的 `myResourceGroup` 资源组来删除这些资源。

若要删除资源组，请执行以下操作：

1. 在 Azure 门户中，导航至左侧导航窗格中的“资源组”  ，然后选择要删除的资源组。
2. 选择“删除资源组”  。 输入资源组名称确认删除并选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署了一个 Azure 区块链服务成员和一个新的联盟。 请尝试学习下一个快速入门，使用适用于 Ethereum 的 Azure 区块链开发工具包将联盟附加到 Azure 区块链服务。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 连接到 Azure 区块链服务](connect-vscode.md)
