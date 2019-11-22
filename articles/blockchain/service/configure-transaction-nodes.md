---
title: 配置 Azure 区块链 Service transaction 节点
description: 如何配置 Azure 区块链 Service transaction 节点
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/20/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 2885e5c9376264875cba03865c45b6b1e5d4aaf2
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286843"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>配置 Azure 区块链 Service transaction 节点

事务节点用于通过公共终结点将区块链事务发送到 Azure 区块链服务。 默认事务节点包含在区块链上注册的以太坊帐户的私钥，因此无法删除。

查看默认事务节点详细信息：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到你的 Azure 区块链服务成员。 选择 "**事务节点**"。

    ![选择默认事务节点](./media/configure-transaction-nodes/nodes.png)

    概述详细信息包括公共终结点地址和公钥。

## <a name="create-transaction-node"></a>创建事务节点

最多可以向区块链成员添加9个附加的事务节点，总计10个事务节点。 通过添加事务节点，可以提高可伸缩性或分配负载。 例如，您可以为不同的客户端应用程序创建一个事务节点终结点。

添加事务节点：

1. 在 Azure 门户中，导航到 Azure 区块链服务成员，然后选择 "**事务节点" > "添加**"。
1. 完成 "新建事务" 节点的设置。

    ![添加事务节点](./media/configure-transaction-nodes/add-node.png)

    | 设置 | 说明 |
    |---------|-------------|
    | 名称 | 事务节点名称。 该名称用于创建事务节点终结点的 DNS 地址。 例如，`newnode-myblockchainmember.blockchain.azure.com`。 创建节点名称后，不能对其进行更改。 |
    | 密码 | 设置强密码。 使用密码访问具有基本身份验证的事务节点终结点。

1. 选择“创建”。

    预配新的事务节点大约需要 10 分钟时间。 其他事务节点会产生费用。 有关成本的详细信息，请参阅[Azure 定价](https://aka.ms/ABSPricing)。

## <a name="endpoints"></a>终结点

事务节点具有唯一的 DNS 名称和公共终结点。

查看事务节点的终结点详细信息：

1. 在 Azure 门户中，导航到 Azure 区块链 Service 成员事务节点之一，然后选择 "**概述**"。

    ![终结点](./media/configure-transaction-nodes/endpoints.png)

事务节点终结点是安全的，需要进行身份验证。 你可以使用 Azure AD 身份验证、HTTPS 基本身份验证以及通过 SSL 上的访问密钥或基于 SSL 的 Websocket 来连接到事务终结点。

### <a name="azure-active-directory-access-control"></a>Azure Active Directory 访问控制

Azure 区块链 Service transaction 节点终结点支持 Azure Active Directory （Azure AD）身份验证。 可以向 Azure AD 用户、组和服务主体授予对终结点的访问权限。

授予对终结点的 Azure AD 访问控制权限：

1. 在 Azure 门户中，导航到 Azure 区块链服务成员，然后选择 "**事务节点" > 访问控制（IAM） > 添加 > "添加角色分配**"。
1. 为用户、组或服务主体（应用程序角色）创建新的角色分配。

    ![添加 IAM 角色](./media/configure-transaction-nodes/add-role.png)

    | 设置 | 操作 |
    |---------|-------------|
    | 角色 | 选择 "**所有者**"、"**参与者**" 或 "**读者**"。
    | 将访问权限分配到 | 选择**Azure AD 用户、组或服务主体**。
    | Select | 搜索要添加的用户、组或服务主体。

1. 选择 "**保存**" 以添加角色分配。

有关 Azure AD 访问控制的详细信息，请参阅[使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限](../../role-based-access-control/role-assignments-portal.md)

有关如何使用 Azure AD 身份验证进行连接的详细信息，请参阅[使用 AAD 身份验证连接到你的节点](configure-aad.md)。

### <a name="basic-authentication"></a>基本身份验证

对于 HTTPS 基本身份验证，用户名和密码凭据会在请求的 HTTPS 标头中传递到终结点。

可以在 Azure 门户中查看事务节点的基本身份验证终结点详细信息。 导航到 Azure 区块链 Service 成员事务节点之一，并在 "设置" 中选择 "**基本身份验证**"。

![基本身份验证](./media/configure-transaction-nodes/basic.png)

用户名是节点的名称，无法更改。

若要使用 URL，请将 \<password\> 替换为预配节点时设置的密码。 可以通过选择 "**重置密码**" 来更新密码。

### <a name="access-keys"></a>访问密钥

对于访问密钥身份验证，访问密钥包含在终结点 URL 中。 预配事务节点后，将生成两个访问密钥。 "访问密钥" 可用于身份验证。 使用两个密钥可以更改和旋转密钥。

您可以查看事务节点的访问密钥详细信息并复制包含访问密钥的终结点地址。 导航到 Azure 区块链 Service 成员事务节点之一，然后在 "设置" 中选择 "**访问密钥**"。

### <a name="firewall-rules"></a>防火墙规则

防火墙规则使你可以限制可尝试对事务节点进行身份验证的 IP 地址。  如果没有为事务节点配置防火墙规则，任何参与方都不能访问它。  

若要查看事务节点的防火墙规则，请导航到 Azure 区块链 Service 成员事务节点之一，并在 "设置" 中选择 "**防火墙规则**"。

可以通过在 "**防火墙规则**" 网格中输入规则名称、"起始 ip 地址" 和 "结束 ip 地址" 来添加防火墙规则。

![防火墙规则](./media/configure-transaction-nodes/firewall-rules.png)

若要启用：

* **单个 IP 地址：** 为起始 IP 地址和结束 IP 地址配置相同的 IP 地址。
* **IP 地址范围：** 配置起始和结束 IP 地址范围。 例如，从10.221.34.0 开始，到10.221.34.255 结束的范围会启用整个10.221.34.xxx 子网。
* **允许所有 IP 地址：** 将 "起始 IP 地址" 配置为0.0.0.0，并将 "结束 IP 地址" 配置为255.255.255.255。

## <a name="connection-strings"></a>连接字符串

为基本身份验证或使用访问密钥提供了事务节点的连接字符串语法。 提供了包括通过 HTTPS 和 Websocket 的访问密钥的连接字符串。

您可以查看事务节点的连接字符串并复制终结点地址。 导航到 Azure 区块链 Service 成员事务节点之一，并在 "设置" 中选择 "**连接字符串**"。

![连接字符串](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>代码示例

提供的示例代码可通过 Web3、Nethereum、Web3js 和 Truffle 快速启用到事务节点的连接。

您可以查看事务节点的示例连接代码，并将其复制到使用常用的开发人员工具。 中转到 Azure 区块链 Service 成员事务节点之一，并在 "设置" 中选择 "**代码示例**"。

选择 Web3、Nethereum、Truffle 或 Web3j 选项卡以查看要使用的代码示例。

![代码示例](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure CLI 配置事务节点](manage-cli.md)
