---
title: Azure 区块链服务安全
description: Azure 区块链服务数据访问和安全概念
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 00d4911c0f2541ea5c64eccca3ab1b1505e06390
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608529"
---
# <a name="azure-blockchain-service-security"></a>Azure 区块链服务安全

Azure 区块链服务使用多个 Azure 功能来保证数据的安全性和可用性。 使用隔离、加密和身份验证来保护数据。

## <a name="isolation"></a>隔离

Azure 区块链服务资源隔离于专用虚拟网络中。 每个事务和验证节点均为虚拟机 (VM)。 一个虚拟网络中的 Vm 无法直接与其他虚拟网络中的 Vm 通信。 隔离可确保通信在虚拟网络中保持私有。 有关 Azure 虚拟网络隔离的详细信息, 请参阅[Azure 公有云中的隔离](../../security/fundamentals/isolation-choices.md#networking-isolation)。

![VNET 关系图](./media/data-security/vnet.png)

## <a name="encryption"></a>加密

用户数据存储在 Azure 存储中。 用户数据以动态和静态加密, 以保证安全性和保密性。 有关详细信息，请参阅：[Azure 存储安全指南](../../storage/common/storage-security-guide.md)。

## <a name="authentication"></a>身份验证

可以通过 RPC 终结点将事务发送到区块链节点。 客户端使用反向代理服务器与事务节点通信, 该服务器处理用户身份验证并通过 SSL 对数据进行加密。

![身份验证关系图](./media/data-security/authentication.png)

有三种用于 RPC 访问的身份验证模式。

### <a name="basic-authentication"></a>基本验证

基本身份验证使用包含用户名和密码的 HTTP authentication 标头。 用户名是区块链节点的名称。 密码是在配置成员或节点的过程中设置的。 可以使用 Azure 门户或 CLI 更改密码。

### <a name="access-keys"></a>访问密钥

访问键使用终结点 URL 中包含的随机生成的字符串。 两个访问键有助于启用密钥旋转。 可以从 Azure 门户和 CLI 重新生成密钥。

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) 使用基于声明的身份验证机制, 其中用户通过使用 Azure AD 用户凭据 Azure AD 进行身份验证。 Azure AD 提供基于云的标识管理, 允许客户在整个企业中使用单个标识并访问云中的应用程序。 Azure 区块链 Service 与 Azure AD 启用 ID 联合、单一登录和多重身份验证相集成。 你可以为组织中的用户、组和应用程序角色分配区块链成员和节点访问权限。

[GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases)上提供了 Azure AD 客户端代理。 客户端代理会将用户定向到 Azure AD 登录页, 并在身份验证成功后获取持有者令牌。 然后, 用户将以太坊客户端应用程序 (例如 Geth 或 Truffle) 连接到客户端代理的终结点。 最后, 在提交事务时, 客户端代理会在 http 标头中注入持有者令牌, 并且反向代理使用 OAuth 协议来验证令牌。

## <a name="keys-and-ethereum-accounts"></a>密钥和以太坊帐户

预配 Azure 区块链服务成员时, 会生成一个以太坊帐户以及一个公钥和私钥对。 私钥用于将事务发送到区块链。 以太坊帐户是公钥哈希的最后20个字节。 以太坊帐户也称为钱包。

私有密钥对以 JSON 格式存储为密钥存储。 使用在创建区块链分类帐服务时输入的密码对私钥进行加密。

私钥用于对事务进行数字签名。 在 private 区块链中, 由私钥签名的智能协定表示签名者的标识。 为了验证签名的有效性, 接收方可以将签名者的公钥与通过签名计算得出的地址进行比较。

星座密钥用于唯一地标识仲裁节点。 星座密钥是在预配节点时生成的, 在仲裁的专用事务的 privateFor 参数中指定。

## <a name="next-steps"></a>后续步骤

[配置 Azure 区块链 Service transaction 节点](configure-transaction-nodes.md)
