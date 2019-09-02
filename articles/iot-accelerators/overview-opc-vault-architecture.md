---
title: OPC 保管库体系结构 - Azure | Microsoft Docs
description: OPC 保管库证书管理服务体系结构
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9331473402ddd22180df3b404824969360d48164
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69995820"
---
# <a name="opc-vault-architecture"></a>OPC 保管库体系结构

本文提供有关 **OPC 保管库微服务**和 **OPC 保管库 IoT Edge 模块**的概述。

## <a name="overview"></a>概述

OPC UA 应用程序使用应用程序实例证书来提供应用程序级别的安全性。 安全连接是使用非对称加密建立的，应用程序证书为此连接提供公钥和私钥对。 证书可以是自签名证书，也可以是证书颁发机构 (CA) 签名的证书。

OPC UA 应用程序提供受信任证书（表示它所信任的应用程序）的列表。 这些证书可以是自签名证书、CA 签名的证书，或者根 CA 或子 CA 本身。 如果受信任的证书是较大证书链的一部分，则应用程序将信任与信任列表中的证书相链接的所有证书，前提是可以验证完整的证书链。

信任自签名证书与信任 CA 证书之间的主要区别在于部署和维护信任所需的安装工作量以及托管公司特定 CA 所需的附加工作量。 

若要使用单个客户端应用程序分发 n 台服务器的自签名证书的信任，必须在客户端应用程序信任列表中安装所有 n 个服务器应用程序证书，且必须在所有服务器应用程序信任列表中安装客户端应用程序证书。 这种管理工作相当繁重，在需要考虑证书生存期和续订证书时，工作量甚至会进一步增大。

使用公司特定的 CA 可以大幅简化与多个服务器和客户端之间的信任管理。 在这种情况下，管理员可为使用的每个客户端和服务器生成 CA 签名的应用程序实例证书一次。 此外，CA 证书安装在所有服务器和客户端上的每个应用程序信任列表中。 如果使用这种方法，只需续订已过期的证书，并为受影响的应用程序替换证书。

Azure 工业 IoT OPC UA 证书管理服务解决方案可以管理基于 OPC 保管库微服务的 OPC UA 应用程序的公司特定 CA。

OPC 保管库提供一个微服务用于在安全云中托管公司特定的 CA，该云由 Azure AD 使用 Azure Key Vault、硬件安全模块、Cosmos DB 和用作应用程序存储的 IoT 中心（可选）保护的服务提供支持。

OPC 保管库微服务旨在支持基于角色的工作流，其中，OT 个人将请求签名的应用程序证书，在 Azure Key Vault 中拥有签名权限的安全管理员和审批者可以批准或拒绝这些请求。

为了与现有的基于 OPC UA GDS 的 OT 解决方案保持兼容，这些服务支持以 OPC 保管库微服务为后盾的 Edge 模块，该模块可实现 *OPC UA 全局发现服务器和证书管理*接口，以根据规范的第 12 部分分发证书和信任列表。 但是，由于我们的知识有限，此 GDS 服务器接口尚未得到广泛使用，并且在功能上仍有限制（“读取者”角色）。 [我们会根据客户的需求改善体验 (*)](#yet-unsupported-features)。

## <a name="architecture"></a>体系结构

该体系结构基于 OPC 保管库微服务，其中包含工厂网络的 OPC 保管库 IoT Edge 模块，以及用于控制工作流的 Web 示例 UX：

![OPCVault 体系结构](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>OPC 保管库微服务

OPC 保管库微服务中的以下接口可以实施为 OPC UA 应用程序分发和管理公司特定 CA 的工作流：

### <a name="application"></a>应用程序 
- “OPC UA 应用程序”可以是服务器和/或客户端。 在这种情况下，OPC 保管库充当应用程序注册机构。 
- 除了用于注册、更新和取消注册应用程序的基本操作以外，还有一些接口可用于通过搜索表达式查找和查询应用程序。 
- 证书请求需要引用有效的应用程序才能处理请求，以及使用所有 OPC UA 特定的扩展来颁发签名证书。 
- 应用程序服务由 CosmosDB 数据库或 [OpcTwin 设备注册表 (*)](#yet-unsupported-features) 提供支持，具体取决于客户配置。

### <a name="certificate-group"></a>证书组
- 证书组是用于存储根 CA 或子 CA 证书（包括用于签署证书的私钥）的实体。 
- 对于颁发者 CA 和签名的应用程序证书，RSA 密钥长度、SHA-2 哈希长度和生存期是可配置的。 
- 可以在单个服务中托管多个组，以便将来使用 https、用户或 ECC 算法证书组 [(*)](#yet-unsupported-features) 进行扩展。 
- CA 证书存储在使用 FIPS 140-2 级别 2 硬件安全模块 (HSM) 支持的 Azure Key Vault 中。 私钥永远不会离开安全存储，因为签名是通过 AzureAD 保护的 Key Vault 操作完成的。 
- 由于维护了 Key Vault 历史记录，CA 证书可随时续订，并保留在安全存储中。 
- 每个 CA 证书的吊销列表也作为机密存储在 Key Vault 中。 取消注册应用程序后，管理员还会在 CRL 中吊销应用程序证书。
- 支持批量吊销和单个证书吊销。

### <a name="certificate-request"></a>证书请求
证书请求实施使用 OPC UA 应用程序的“证书签名请求”(CSR) 生成新密钥对或签名证书的工作流。 
- 该请求连同附带信息（例如使用者或 CSR，以及对 OPC UA 应用程序的引用）一起存储在数据库中。 
- 服务中的业务逻辑根据应用程序数据库中存储的信息验证该请求。 例如，数据库中的应用程序 URI 必须与 CSR 中的应用程序 URI 相匹配。
- 拥有签名权限（“审批者”角色）的安全管理员可以批准或拒绝请求。 如果请求获得批准，则会生成新的密钥对和/或签名的证书。 新私钥安全存储在 KeyVault 中，新的已签名公共证书存储在证书请求数据库中。
- 在审批或撤销请求之前，请求者可以轮询其状态。 如果请求获得批准，则可以下载私钥和证书，并将其安装在 OPC UA 应用程序的证书存储中。
- 现在，请求者可以接受从请求数据库中删除不必要的信息的请求。 

在已签名证书的生存期内，某个应用程序可能被删除，或者某个密钥可能已透露。 在这种情况下，CA 管理者可以：
- 删除应用程序，同时删除该应用的所有待处理和已批准的证书请求。 
- 另一种做法是仅当密钥已续订或透露时才删除单个证书请求。
- 现在，已透露的已批准和已接受证书请求将标记为已删除。
- 管理者可以定期执行颁发者 CA CRL 的续订。 在续订时，所有已删除的证书请求将被撤销，证书序列号将添加到 CRL 吊销列表。 已撤销的证书请求将标记为“已撤销”。
- 发生紧急事件时，也可以撤销单个证书请求。
- 最后，已更新的 CRL 可分发到参与方 OPC UA 客户端和服务器。

有关 OPC 保管库微服务 API 的详细信息，请参阅该微服务的 swagger 文档。

## <a name="opc-vault-iot-edge-module"></a>OPC 保管库 IoT Edge 模块
若要支持工厂网络全局发现服务器，可以在 Edge 上部署 OPC 保管库模块，并将其作为本地 .Net Core 应用程序执行，或者在 Docker 容器中启动。 由于当前的 OPC UA .Net Standard 堆栈中缺少 Auth2 身份验证支持，并且无法使用 OPC UA GDS 标准接口模拟从 Edge 模块到微服务的用户操作，因此 OPC 保管库 Edge 模块的功能仅限“读取者”角色使用。 目前，只允许执行不需要“写入者”、“管理员”或“审批者”角色的操作 [(*)](#yet-unsupported-features)。 

## <a name="yet-unsupported-features"></a>尚不支持的功能

**(*)** 尚不支持。

## <a name="next-steps"></a>后续步骤

了解 OPC 保管库体系结构后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [构建和部署 OPC 保管库](howto-opc-vault-deploy.md)
