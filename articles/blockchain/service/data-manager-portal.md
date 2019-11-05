---
title: 配置区块链数据管理器-Azure 门户
description: 如何使用 Azure 门户创建和管理区块链数据管理器。
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: 1f46fe92fd6650daa3ba4b9a930c4d781925d3fc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518250"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>使用 Azure 门户配置区块链数据管理器

将 Azure 区块链服务的区块链数据管理器配置为捕获区块链数据并将其发送到 Azure 事件网格主题。

若要配置区块链数据管理器实例，你需要：

* 为 Azure 区块链 Service transaction 节点创建区块链数据管理器实例
* 添加区块链应用程序

## <a name="prerequisites"></a>必备组件

* 完成[快速入门：使用 Azure 门户或快速入门创建区块链成员](create-member.md) [：使用 Azure CLI 创建 Azure 区块链 Service 区块链成员](create-member-cli.md)
* 创建[事件网格主题](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* 了解[Azure 事件网格中的事件处理程序](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>创建实例

区块链数据管理器实例连接并监视 Azure 区块链服务事务节点。 只有有权访问事务节点的用户才能创建连接。 实例从事务节点捕获所有原始块和原始事务数据。

出站连接将区块链数据发送到 Azure 事件网格。 创建实例时，可以配置单个出站连接。 对于任何给定的区块链数据管理器实例，区块链数据管理器支持多个事件网格主题出站连接。 可以将区块链数据发送到单个目标，或将区块链数据发送到多个目标。 若要添加另一个目标，只需向实例添加其他出站连接即可。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请参阅要连接到区块链数据管理器的 Azure 区块链服务成员。 选择**区块链数据管理器**。
1. 选择“设置”（应用程序对象和服务主体对象）。

    ![添加区块链数据管理器](./media/data-manager-portal/add-instance.png)

    输入以下详细信息：

    设置 | 说明
    --------|------------
    名称 | 为连接的区块链数据管理器输入唯一的名称。 区块链数据管理器名称可以包含小写字母和数字，并且最大长度为20个字符。
    事务节点 | 选择事务节点。 仅列出具有读取访问权限的事务节点。
    连接名称 | 输入用于发送区块链事务数据的出站连接的唯一名称。
    事件网格端点 | 选择与区块链数据管理器实例相同的订阅中的事件网格主题。

1. 选择“确定”。

    创建区块链数据管理器实例需要不到一分钟的时间。 部署实例后，它会自动启动。 正在运行的区块链数据管理器实例从事务节点捕获区块链事件，并将数据发送到出站连接。 如果还希望从事务节点捕获解码的事件和属性数据，请为区块链数据管理器实例创建区块链应用程序。

    新实例将出现在 Azure 区块链服务成员的区块链数据管理器实例列表中。

    ![区块链数据成员实例列表](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>添加区块链应用程序

如果添加区块链应用程序，则区块链数据管理器对应用程序的事件和属性状态进行解码。 否则，只会发送原始块和原始事务数据。 区块链数据管理器还会在部署协定时发现协定地址。 可以将多个区块链应用程序添加到区块链数据管理器实例。

区块链数据管理器需要智能协定 ABI 和字节码文件来添加应用程序。

### <a name="get-contract-abi-and-bytecode"></a>获取协定 ABI 和字节码

协定 ABI 定义智能协定接口。 它介绍了如何与智能协定交互。 可以使用[用于以太坊扩展的 Azure 区块链开发工具包](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)将协定 ABI 复制到剪贴板。

1. 在 "Visual Studio Code 资源管理器" 窗格中，展开 "密度" 项目的 "**生成"/"协定**" 文件夹。
1. 右键单击协定元数据 JSON 文件。 文件名为智能协定名称，后跟**json**扩展名。
1. 选择 "**复制协定 ABI**"。

    ![带有复制协定 ABI 选择的 Visual Studio Code 窗格](./media/data-manager-portal/abi-devkit.png)

    将协定 ABI 复制到剪贴板。

1. 将**abi**数组保存为 JSON 文件。 例如， *abi*。 稍后的步骤中会用到该文件。

协定字节码是以太坊虚拟机执行的编译的智能协定。 可以使用扩展将协定字节码复制到剪贴板。

1. 在 "Visual Studio Code 资源管理器" 窗格中，展开 "密度" 项目的 "**生成"/"协定**" 文件夹。
1. 右键单击协定元数据 JSON 文件。 文件名为智能协定名称，后跟**json**扩展名。
1. 选择 "**复制协定字节码**"。

    ![带有复制协定字节码选择的 Visual Studio Code 窗格](./media/data-manager-portal/bytecode-devkit.png)

    协定字节码将被复制到剪贴板。

1. 将**字节码**值保存为 JSON 文件。 例如，*字节码。* 仅保存十六进制值。 稍后的步骤中会用到该文件。

下面的示例演示如何在 VS Code 编辑器中打开*abi*和*字节文件。* 文件应类似于。

![Abi 和字节码 json 文件的示例](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>创建协定 ABI 和字节码 URL

区块链数据管理器要求在添加应用程序时 URL 可以访问协定 ABI 和字节码文件。 可以使用 Azure 存储帐户提供可私下访问的 URL。

#### <a name="create-storage-account"></a>创建存储帐户

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>上传协定文件

1. 为存储帐户创建新的容器。 选择容器 **> 容器**。

    ![创建存储帐户容器](./media/data-manager-portal/create-container.png)

    | 字段 | 说明 |
    |-------|-------------|
    | 名称  | 命名该容器。 例如， *smartcontract* |
    | 公共访问级别 | 选择*专用（无匿名访问）* |

1. 选择“确定”创建容器。
1. 选择容器，然后选择 "**上传**"。
1. 选择在[获取协定 ABI 和字节码](#get-contract-abi-and-bytecode)部分中创建的两个 JSON 文件。

    ![上传 blob](./media/data-manager-portal/upload-blobs.png)

    选择“上传”。

#### <a name="generate-url"></a>生成 URL

为每个 blob 生成共享访问签名。

1. 选择 ABI JSON blob。
1. 选择 "**生成 SAS** "
1. 设置所需的访问签名过期时间，然后选择 "**生成 BLOB SAS 令牌和 URL**"。

    ![生成 SAS 令牌](./media/data-manager-portal/generate-sas.png)

1. 复制**BLOB SAS URL** ，并将其保存在下一部分。
1. 重复为字节码 JSON blob[生成 URL](#generate-url)步骤。

### <a name="add-application-to-instance"></a>向实例添加应用程序

1. 从实例列表中选择区块链数据管理器实例。
1. 选择**区块链应用程序**。
1. 选择“设置”（应用程序对象和服务主体对象）。

    ![添加区块链应用程序](./media/data-manager-portal/add-application.png)

    输入区块链应用程序的名称以及智能协定 ABI 和字节码 Url。

    设置 | 说明
    --------|------------
    名称 | 输入要跟踪的区块链应用程序的唯一名称。
    协定 ABI | 协定 ABI 文件的 URL 路径。 有关详细信息，请参阅[创建协定 ABI 和字节码 URL](#create-contract-abi-and-bytecode-url)。
    协定字节码 | 字节码文件的 URL 路径。 有关详细信息，请参阅[创建协定 ABI 和字节码 URL](#create-contract-abi-and-bytecode-url)。

1. 选择“确定”。

    创建应用程序后，应用程序将显示在区块链应用程序的列表中。

    ![区块链应用程序列表](./media/data-manager-portal/artifact-list.png)

可以删除 Azure 存储帐户，也可以使用它来配置更多区块链应用程序。 如果要删除 Azure 存储帐户，可以删除该资源组。 删除资源组也会删除相关联的存储帐户，以及与资源组相关联的任何其他资源。

## <a name="stop-instance"></a>停止实例

要停止捕获区块链事件并将数据发送到出站连接时，请停止区块链 Manager 实例。 实例停止后，区块链数据管理器不会产生费用。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/blockchain-service)。

1. 请参阅 "**概述**" 并选择 "**停止**"。

    ![停止实例](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>后续步骤

详细了解[Azure 事件网格中的事件处理程序](../../event-grid/event-handlers.md)。
