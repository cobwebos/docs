---
title: Azure 区块链服务安全性
description: Azure 区块链服务数据访问和安全概念
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 06bf4e0fa4037b07505a4f816fc7af56c14576d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982231"
---
# <a name="azure-blockchain-service-security"></a>Azure 区块链服务安全性

Azure 区块链服务使用多个 Azure 功能来保证数据的安全性和可用性。 数据通过隔离、加密和身份验证进行保护。

## <a name="isolation"></a>隔离

Azure 区块链服务资源在专用虚拟网络中隔离。 每个事务和验证节点都是虚拟机 （VM）。 一个虚拟网络中的 VM 无法直接与不同虚拟网络中的 VM 通信。 隔离可确保虚拟网络中的通信保持私密性。 有关 Azure 虚拟网络隔离的详细信息，请参阅[Azure 公共云中的隔离](../../security/fundamentals/isolation-choices.md#networking-isolation)。

![VNET 图](./media/data-security/vnet.png)

## <a name="encryption"></a>加密

用户数据存储在 Azure 存储中。 用户数据在移动和静态中加密，以增强安全性和机密性。 有关详细信息，请参阅[：Azure 存储安全指南](../../storage/blobs/security-recommendations.md)。

## <a name="authentication"></a>身份验证

事务可以通过 RPC 终结点发送到区块链节点。 客户端使用反向代理服务器与事务节点通信，该服务器处理用户身份验证并通过 SSL 加密数据。

![身份验证图](./media/data-security/authentication.png)

RPC 访问有三种身份验证模式。

### <a name="basic-authentication"></a>基本身份验证

基本身份验证使用包含用户名和密码的 HTTP 身份验证标头。 用户名是区块链节点的名称。 密码是在成员或节点的预配期间设置的。 可以使用 Azure 门户或 CLI 更改密码。

### <a name="access-keys"></a>访问密钥

访问键使用终结点 URL 中包含的随机生成的字符串。 两个访问密钥有助于启用密钥轮换。 可以从 Azure 门户和 CLI 重新生成密钥。

### <a name="azure-active-directory"></a>Azure Active Directory

Azure 活动目录 （Azure AD） 使用基于声明的身份验证机制，其中用户由 Azure AD 使用 Azure AD 用户凭据进行身份验证。 Azure AD 提供基于云的标识管理，并允许客户在整个企业中使用单个标识并访问云上的应用程序。 Azure 区块链服务与 Azure AD 支持 ID 联合、单一登录和多重身份验证集成。 您可以为组织中的用户、组和应用程序角色分配区块链成员和节点访问。

Azure AD 客户端代理在[GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases)上可用。 客户端代理将用户定向到 Azure AD 登录页，并在成功身份验证后获取无记名令牌。 随后，用户将以来者客户端应用程序（如 Geth 或 Truffle）连接到客户端代理的终结点。 最后，在提交事务时，客户端代理在 http 标头中注入无记名令牌，反向代理使用 OAuth 协议验证令牌。

## <a name="keys-and-ethereum-accounts"></a>钥匙和以地庭帐户

预配 Azure 区块链服务成员时，将生成以天坊帐户以及公钥和私钥对。 私钥用于将事务发送到区块链。 以地庭帐户是公钥哈希的最后 20 个字节。 以子坊帐户也称为钱包。

私钥和公钥对以 JSON 格式存储为密钥文件。 私钥使用创建区块链分类账服务时输入的密码进行加密。

私钥用于对事务进行数字签名。 在私有区块链中，由私钥签署的智能合约代表签名者的身份。 为了验证签名的有效性，接收方可以将签名器的公钥与从签名计算的地址进行比较。

星座键用于唯一标识仲裁节点。 星座密钥在节点预配时生成，并在 Quorum 中专用事务的私有 For 参数中指定。

## <a name="next-steps"></a>后续步骤

请参阅[如何为 Azure 区块链服务配置 Azure 活动目录访问](configure-aad.md)。
