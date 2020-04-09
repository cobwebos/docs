---
title: 配置 Azure 区块链服务事务节点
description: 如何配置 Azure 区块链服务事务节点
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 8fa18496d0c1aa59beb55569e731967d5ebea427
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876923"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>配置 Azure 区块链服务事务节点

事务节点用于通过公共终结点将区块链事务发送到 Azure 区块链服务。 默认事务节点包含在区块链上注册的以地庭帐户的私钥，因此无法删除。

要查看默认事务节点详细信息：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 导航到你的 Azure 区块链服务成员。 选择**事务节点**。

    ![选择默认事务节点](./media/configure-transaction-nodes/nodes.png)

    概述详细信息包括公共终结点地址和公钥。

## <a name="create-transaction-node"></a>创建事务节点

您最多可以将 9 个附加交易节点添加到区块链成员中，总共 10 个事务节点。 通过添加事务节点，可以增加可伸缩性或分配负载。 例如，您可以为不同的客户端应用程序提供事务节点终结点。

要添加事务节点：

1. 在 Azure 门户中，导航到 Azure 区块链服务成员，然后**选择事务节点>添加**。
1. 完成新事务节点的设置。

    ![添加事务节点](./media/configure-transaction-nodes/add-node.png)

    | 设置 | 说明 |
    |---------|-------------|
    | 名称 | 事务节点名称。 该名称用于创建事务节点终结点的 DNS 地址。 例如，`newnode-myblockchainmember.blockchain.azure.com` 。 创建节点名称后，无法更改节点名称。 |
    | 密码 | 设置强密码。 使用密码使用基本身份验证访问事务节点终结点。

1. 选择“创建”  。

    预配新的事务节点大约需要 10 分钟时间。 其他事务节点会产生成本。 有关成本的详细信息，请参阅[Azure 定价](https://aka.ms/ABSPricing)。

## <a name="endpoints"></a>终结点

事务节点具有唯一的 DNS 名称和公共终结点。

要查看事务节点的终结点详细信息：

1. 在 Azure 门户中，导航到 Azure 区块链服务成员事务节点之一，然后选择 **"概述**"。

    ![终结点](./media/configure-transaction-nodes/endpoints.png)

事务节点终结点是安全的，需要身份验证。 您可以使用 Azure AD 身份验证、HTTPS 基本身份验证以及通过 HTTPS 或 Web套接字通过 TLS 访问密钥连接到事务终结点。

### <a name="azure-active-directory-access-control"></a>Azure 活动目录访问控制

Azure 区块链服务事务节点终结点支持 Azure 活动目录 （Azure AD） 身份验证。 您可以授予 Azure AD 用户、组和服务主体对终结点的访问权限。

要向终结点授予 Azure AD 访问控制，请执行：

1. 在 Azure 门户中，导航到 Azure 区块链服务成员，然后选择**事务节点>访问控制 （IAM） >添加>添加角色分配**。
1. 为用户、组或服务主体（应用程序角色）创建新的角色分配。

    ![添加 IAM 角色](./media/configure-transaction-nodes/add-role.png)

    | 设置 | 操作 |
    |---------|-------------|
    | 角色 | 选择**所有者**、**参与者**或**读取器**。
    | 将访问权限分配到 | 选择**Azure AD 用户、组或服务主体**。
    | Select | 搜索要添加的用户、组或服务主体。

1. 选择 **"保存"** 以添加角色分配。

有关 Azure AD 访问控件的详细信息，请参阅[使用 RBAC 和 Azure 门户管理对 Azure 资源的访问](../../role-based-access-control/role-assignments-portal.md)

有关如何使用 Azure AD 身份验证进行连接的详细信息，请参阅[使用 AAD 身份验证连接到节点](configure-aad.md)。

### <a name="basic-authentication"></a>基本身份验证

对于 HTTPS 基本身份验证，用户名和密码凭据在请求的 HTTPS 标头中传递给终结点。

您可以在 Azure 门户中查看事务节点的基本身份验证终结点详细信息。 导航到 Azure 区块链服务成员事务节点之一，并在设置中选择 **"基本身份验证**"。

![基本身份验证](./media/configure-transaction-nodes/basic.png)

用户名是节点的名称，无法更改。

要使用 URL，请将\<密码\>替换为预配节点时设置的密码。 您可以通过选择 **"重置密码"** 来更新密码。

### <a name="access-keys"></a>访问密钥

对于访问密钥身份验证，访问密钥包含在终结点 URL 中。 预配事务节点时，将生成两个访问密钥。 任一访问密钥都可用于身份验证。 两个键使您能够更改和旋转键。

您可以查看事务节点的访问密钥详细信息，并复制包含访问密钥的终结点地址。 导航到 Azure 区块链服务成员事务节点之一，并在设置中选择**访问密钥**。

### <a name="firewall-rules"></a>防火墙规则

防火墙规则使您能够限制可以尝试向事务节点进行身份验证的 IP 地址。  如果未为事务节点配置防火墙规则，则任何一方都无法访问该防火墙规则。  

要查看事务节点的防火墙规则，请导航到 Azure 区块链服务成员事务节点之一，并在设置中选择**防火墙规则**。

您可以通过在防火墙规则网格中输入规则名称、启动 IP 地址和结束 IP 地址来添加**防火墙规则**。

![防火墙规则](./media/configure-transaction-nodes/firewall-rules.png)

若要启用：

* **单个 IP 地址：** 为启动和结束 IP 地址配置相同的 IP 地址。
* **IP 地址范围：** 配置启动和结束 IP 地址范围。 例如，从 10.221.34.0 开始到 10.221.34.255 结束的范围将启用整个10.221.34.xxx子网。
* **允许所有 IP 地址：** 将起始 IP 地址配置为 0.0.0.0，将结束 IP 地址配置为 255.255.255.255。

## <a name="connection-strings"></a>连接字符串

为事务节点提供连接字符串语法以进行基本身份验证或使用访问密钥。 提供连接字符串，包括 HTTPS 和 WebSocket 的访问密钥。

您可以查看事务节点的连接字符串和复制终结点地址。 导航到 Azure 区块链服务成员事务节点之一，并在设置中选择**连接字符串**。

![连接字符串](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>示例代码

提供示例代码可快速启用通过 Web3、Nethereum、Web3js 和 Truffle 连接到事务节点。

您可以查看事务节点的示例连接代码并将其复制到常用开发人员工具中。 转到 Azure 区块链服务成员事务节点之一，并在设置中选择**示例代码**。

选择 Web3、内蒂图姆、特鲁弗尔或 Web3j 选项卡以查看要使用的代码示例。

![示例代码](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure CLI 配置事务节点](manage-cli.md)
