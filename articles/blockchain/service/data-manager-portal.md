---
title: 使用 Azure 门户配置区块链数据管理器-Azure 区块链服务
description: 使用 Azure 门户创建和管理 Azure 区块链服务的区块链数据管理器。
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 9c682f449fbab823134d626870c7dcfe8a8f2847
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455811"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>使用 Azure 门户配置区块链数据管理器

将 Azure 区块链服务的区块链数据管理器配置为捕获区块链数据并将其发送到 Azure 事件网格主题。

若要配置区块链数据管理器实例，你需要：

* 为 Azure 区块链 Service transaction 节点创建区块链数据管理器实例
* 添加区块链应用程序

## <a name="prerequisites"></a>先决条件

* 完成[快速入门：使用 Azure 门户或快速入门创建区块链成员](create-member.md) [：使用 Azure CLI 创建 Azure 区块链 Service 区块链成员](create-member-cli.md)
* 创建[事件网格主题](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* 了解 [Azure 事件网格中的事件处理程序](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>创建实例

区块链数据管理器实例连接并监控 Azure 区块链服务事务节点。 只有有权访问事务节点的用户才能创建连接。 实例从事务节点捕获所有原始块和原始事务数据。

出站连接将区块链数据发送到 Azure 事件网格。 创建实例时，可以配置单个出站连接。 区块链数据管理器支持对任意给定的区块链数据管理器实例使用多个事件网格主题出站连接。 可将区块链数据发送到一个或多个目标。 若要添加另一个目标，只需向实例添加其他出站连接即可。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请参阅要连接到区块链数据管理器的 Azure 区块链服务成员。 选择“区块链数据管理器”。
1. 选择“添加”。

    ![添加“区块链数据管理器”](./media/data-manager-portal/add-instance.png)

    输入以下详细信息：

    设置 | 说明
    --------|------------
    名称 | 为连接的区块链数据管理器输入唯一的名称。 区块链数据管理器名称可以包含小写字母和数字，并且最大长度为20个字符。
    事务节点 | 选择事务节点。 仅列出具有读取访问权限的事务节点。
    连接名称 | 输入用于发送区块链事务数据的出站连接的唯一名称。
    事件网格终结点 | 选择与区块链数据管理器实例相同的订阅中的事件网格主题。

1. 选择“确定”。

    创建区块链数据管理器实例需要不到一分钟的时间。 部署实例后，它会自动启动。 正在运行的区块链数据管理器实例从事务节点捕获区块链事件，并将数据发送到出站连接。

    新实例将出现在 Azure 区块链服务成员的区块链数据管理器实例列表中。

    ![区块链数据成员实例列表](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>添加区块链应用程序

如果添加区块链应用程序，则区块链数据管理器对应用程序的事件和属性状态进行解码。 否则，只会发送原始块和原始事务数据。 区块链数据管理器还会在部署协定时发现协定地址。 可以将多个区块链应用程序添加到区块链数据管理器实例。

> [!IMPORTANT]
> 目前，不完全支持声明密度[数组类型](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays)或[映射类型](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types)的区块链应用程序。 声明为数组或映射类型的属性将不会在*ContractPropertiesMsg*或*DecodedContractEventsMsg*消息中进行解码。

区块链数据管理器需要智能协定 ABI 并部署了字节码文件来添加应用程序。

### <a name="get-contract-abi-and-bytecode"></a>获取协定 ABI 和字节码

合同 ABI 定义智能合同接口。 其中描述了如何与智能合同交互。 可以使用[适用于 Ethereum 扩展的 Azure 区块链开发工具包](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)将合同 ABI 复制到剪贴板。

1. 在 Visual Studio Code 资源管理器窗格中，展开 Solidity 项目的 **build/contracts** 文件夹。
1. 右键单击合同元数据 JSON 文件。 文件名为智能合同名称后接 **.json** 扩展名。
1. 选择“复制合同 ABI”。

    ![选择了“复制合同 ABI”的 Visual Studio Code 窗格](./media/data-manager-portal/abi-devkit.png)

    合同 ABI 将复制到剪贴板。

1. 将 abi 数组保存为 JSON 文件。 例如：abi.json。 在稍后的步骤中会使用此文件。

区块链数据管理器需要为智能合同部署的字节码。 部署的字节码不同于智能合同字节码。 可从已编译的合同元数据文件中获取部署的字节码。

1. 打开 Solidity 项目的 **build/contracts** 文件夹中包含的合同元数据文件。 文件名为智能合同名称后接 **.json** 扩展名。
1. 在 JSON 文件中找到“deployedBytecode”元素。
1. 复制不带引号的十六进制值。

    ![元数据中包含字节码的 Visual Studio Code 窗格](./media/data-manager-portal/bytecode-metadata.png)

1. 将“字节码”值保存为 JSON 文件。 例如：“bytecode.json”。 在稍后的步骤中会使用此文件。

下面的示例演示的是如何在 VS Code 编辑器中打开“abi.json”和“bytecode.json”。 文件应看起来很相似。

![abi.json 和 bytecode.json 文件的示例](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>创建合同 ABI 和字节码 URL

添加应用程序时，区块链数据管理器要求由 URL 访问合同 ABI 和字节码文件。 可以使用 Azure 存储帐户提供可私下访问的 URL。

#### <a name="create-storage-account"></a>创建存储帐户

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>上传合同文件

1. 为存储帐户新建容器。 选择“容器”>“容器”。

    ![创建存储帐户容器](./media/data-manager-portal/create-container.png)

    | 字段 | 说明 |
    |-------|-------------|
    | 名称  | 为该容器命名。 例如，“smartcontract” |
    | 公共访问级别 | 选择“专用(没有匿名访问权限)” |

1. 选择“确定”创建容器。
1. 先选择该容器，然后选择“上传”。
1. 选择在[获取合同 ABI 和字节码](#get-contract-abi-and-bytecode)部分中创建的两个 JSON 文件。

    ![上传 blob](./media/data-manager-portal/upload-blobs.png)

    选择“上传”。

#### <a name="generate-url"></a>生成 URL

对于各个 blob，生成共享访问签名。

1. 选择“ABI JSON blob”。
1. 选择“生成 SAS”
1. 设置所需的访问签名到期时间，然后选择“生成 blob SAS 令牌和 URL”。

    ![生成 SAS 令牌](./media/data-manager-portal/generate-sas.png)

1. 复制“Blob SAS URL”，以便在下一部分使用。
1. 重复字节码 JSON blob 的[生成 URL](#generate-url) 步骤。

### <a name="add-application-to-instance"></a>向实例添加应用程序

1. 从实例列表中选择区块链数据管理器实例。
1. 选择“区块链应用程序”。
1. 选择“添加”。

    ![添加区块链应用程序](./media/data-manager-portal/add-application.png)

    输入区块链应用程序的名称以及智能合同 ABI 和字节码 URL。

    设置 | 说明
    --------|------------
    名称 | 输入要跟踪的区块链应用程序的唯一名称。
    合同 ABI | 合同 ABI 文件的 URL 路径。 有关详细信息，请参阅[创建合同 ABI 和字节码 URL](#create-contract-abi-and-bytecode-url)。
    合同字节码 | 字节码文件的 URL 路径。 有关详细信息，请参阅[创建合同 ABI 和字节码 URL](#create-contract-abi-and-bytecode-url)。

1. 选择“确定”。

    创建应用程序后，应用程序将显示在区块链应用程序的列表中。

    ![区块链应用程序列表](./media/data-manager-portal/artifact-list.png)

可以删除 Azure 存储帐户，也可以使用它来配置更多区块链应用程序。 如果要删除 Azure 存储帐户，可以删除该资源组。 删除资源组也会删除相关联的存储帐户，以及与资源组相关联的任何其他资源。

## <a name="stop-instance"></a>停止实例

要停止捕获区块链事件并将数据发送到出站连接时，请停止区块链 Manager 实例。 实例停止后，区块链数据管理器不会产生费用。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/blockchain-service)。

1. 请参阅 "**概述**" 并选择 "**停止**"。

    ![停止实例](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>后续步骤

尝试下一教程使用区块链数据管理器和 Azure Cosmos DB 创建区块链事务消息资源管理器。

> [!div class="nextstepaction"]
> [使用区块链数据管理器向 Azure Cosmos DB 发送数据](data-manager-cosmosdb.md)