---
title: 如何安全地运行 OPC 保管库证书管理服务 - Azure |微软文档
description: 介绍如何在 Azure 中安全地运行 OPC Vault 证书管理服务，并查看其他要考虑的安全准则。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271702"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>安全地运行 OPC 保管库证书管理服务

本文介绍如何在 Azure 中安全地运行 OPC Vault 证书管理服务，并查看其他要考虑的安全准则。

## <a name="roles"></a>角色

### <a name="trusted-and-authorized-roles"></a>受信任和授权的角色

OPC Vault 微服务允许不同角色访问服务的各个部分。

> [!IMPORTANT]
> 在部署期间，脚本仅添加作为所有角色的用户运行部署脚本的用户。 对于生产部署，应查看此角色分配，并遵循以下准则进行适当重新配置。 此任务需要在 Azure 活动目录 （Azure AD） 企业应用程序门户中手动分配角色和服务。

### <a name="certificate-management-service-roles"></a>证书管理服务角色

OPC Vault 微服务定义了以下角色：

- **读取器**：默认情况下，租户中的任何经过身份验证的用户都具有读取访问权限。 
  - 读取对应用程序和证书请求的访问。 可以列出和查询应用程序和证书请求。 此外，设备发现信息和公共证书可通过读取访问访问。
- **编写**器 ：编写器角色分配给用户以添加某些任务的写入权限。 
  - 对应用程序和证书请求的读/写访问。 可以注册、更新和取消注册应用程序。 可以创建证书请求并获得批准的私钥和证书。 也可以删除私钥。
- **审批者**：审批者角色分配给用户以批准或拒绝证书请求。 该角色不包括任何其他角色。
  - 除了访问 OPC Vault 微服务 API 的审批器角色外，用户还必须在 Azure 密钥保管库中具有密钥签名权限才能对证书进行签名。
  - 应将编写器和审批者角色分配给不同的用户。
  - 审批人的主要作用是批准证书请求的生成和拒绝。
- **管理员**：管理员角色分配给用户以管理证书组。 该角色不支持审批者角色，但包括"编写者"角色。
  - 管理员可以通过颁发新的证书吊销列表 （CRL） 来管理证书组、更改配置和撤销应用程序证书。
  - 理想情况下，作者、审批人和管理员角色分配给不同的用户。 为了获得其他安全性，具有审批方或管理员角色的用户还需要密钥保管库中的密钥签名权限，才能颁发证书或续订颁发者 CA 证书。
  - 除了微服务管理角色外，该角色还包括，但不限于：
    - 负责管理 CA 安全实践的实施。
    - 证书的生成、吊销和挂起的管理。 
    - 加密密钥生命周期管理（例如，续订颁发者 CA 密钥）。
    - 安装、配置和维护运行 CA 的服务。
    - 服务的日常运行。 
    - CA 和数据库备份和恢复。

### <a name="other-role-assignments"></a>其他角色分配

在运行服务时，还要考虑以下角色：

- 证书采购合同的业主与外部根证书颁发机构（例如，当所有者从外部 CA 购买证书或操作从属外部 CA 的 CA 时）。
- 证书颁发机构的开发和验证。
- 审查审计记录。
- 帮助支持 CA 或管理物理和云设施，但不受直接信任执行 CA 操作的人员，由*授权*角色担任。 还必须记录授权角色中的任务集。

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>每季度审核受信任和授权角色的成员资格

至少每季度审核一次受信任和授权角色的成员资格。 确保将每个角色中的人员（用于手动流程）或服务标识（用于自动化流程）集保持在最低限度。

### <a name="role-separation-between-certificate-requester-and-approver"></a>证书请求者和审批者之间的角色分离

证书颁发过程必须强制证书请求者和证书审批者角色（人员或自动系统）之间的角色分离。 证书颁发必须由证书审批者角色授权，该角色验证证书请求者是否有权获取证书。 担任证书审批人职务的人员必须是正式授权的人员。

### <a name="restrict-assignment-of-privileged-roles"></a>限制特权角色的分配

您应该将特权角色的分配（如授权管理员和审批者组的成员身份）限制为一组有限的授权人员。 任何特权角色更改都必须在 24 小时内撤消访问权限。 最后，每季度查看特权角色分配，并删除任何不需要或过期的分配。

### <a name="privileged-roles-should-use-two-factor-authentication"></a>特权角色应使用双重身份验证

使用多重身份验证（也称为双重身份验证）对审批者和管理员对服务的交互式登录。

## <a name="certificate-service-operation-guidelines"></a>证书服务操作指南

### <a name="operational-contacts"></a>业务联系人

证书服务必须存档最新的安全响应计划，其中包含详细的操作事件响应联系人。

### <a name="security-updates"></a>安全更新

所有系统都必须使用最新的安全更新进行持续监视和更新。

> [!IMPORTANT]
> OPC Vault 服务的 GitHub 存储库会不断更新安全修补程序。 监视这些更新，并定期将其应用于服务。

### <a name="security-monitoring"></a>安全监视

订阅或实施适当的安全监视。 例如，订阅中央监视解决方案（如 Azure 安全中心或 Office 365 监视解决方案），并对其进行适当配置，以确保安全事件传输到监视解决方案。

> [!IMPORTANT]
> 默认情况下，OPC Vault 服务随[Azure 应用程序见解](https://docs.microsoft.com/azure/azure-monitor/app/devops)一起部署作为监视解决方案。 强烈建议添加 Azure[安全中心](https://azure.microsoft.com/services/security-center/)等安全解决方案。

### <a name="assess-the-security-of-open-source-software-components"></a>评估开源软件组件的安全性

产品或服务中使用的所有开源组件都必须没有中等或更大的安全漏洞。

> [!IMPORTANT]
> 在连续集成生成期间，OPC Vault 服务的 GitHub 存储库会扫描所有组件中的漏洞。 在 GitHub 上监视这些更新，并定期将它们应用于服务。

### <a name="maintain-an-inventory"></a>维护库存

维护所有生产主机（包括持久虚拟机）、设备、所有内部 IP 地址范围、VIP 和公有 DNS 域名的资产清单。 每当添加或删除系统、设备 IP 地址、VIP 或公共 DNS 域时，都必须在 30 天内更新清单。

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>默认 Azure OPC Vault 微服务生产部署的清单 

在 Azure 中：
- **应用服务计划**：服务主机的应用服务计划。 默认 S1。
- 用于微服务**的应用服务**：OPC Vault 服务主机。
- 示例**应用程序的应用服务**：OPC Vault 示例应用程序主机。
- **密钥保管库标准**：用于存储 Web 服务的秘密和 Azure Cosmos DB 密钥。
- **密钥保管库高级**：承载颁发者 CA 密钥、签名服务以及保管库配置和应用程序私钥的存储。
- **Azure 宇宙数据库**：用于应用程序和证书请求的数据库。 
- **应用程序见解**：（可选）用于 Web 服务和应用程序的监视解决方案。
- **Azure AD 应用程序注册**：示例应用程序、服务和边缘模块的注册。

对于云服务，应记录用于部署服务的所有主机名、资源组、资源名称、订阅 ID 和租户 ID。 

在 Azure IoT 边缘或本地 IoT 边缘服务器中：
- **OPC Vault IoT 边缘模块**：支持工厂网络 OPC UA 全局发现服务器。 

对于 IoT 边缘设备，应记录主机名和 IP 地址。 

### <a name="document-the-certification-authorities-cas"></a>记录认证机构 （C）

CA 层次结构文档必须包含所有操作的 CA。 这包括所有相关的从属 C、父 C 和根 C，即使它们不是由服务管理也是如此。 您可以提供一组详尽无遗的所有未过期 CA 证书，而不是正式文档。

> [!NOTE]
> OPC Vault 示例应用程序支持下载服务中使用的和生成的所有证书以进行文档记录。

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>记录所有认证机构 （C） 颁发的证书

提供过去 12 个月内签发的所有证书的详尽集。

> [!NOTE]
> OPC Vault 示例应用程序支持下载服务中使用的和生成的所有证书以进行文档记录。

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>记录安全删除加密密钥的标准操作过程

在 CA 的生存期内，密钥删除可能很少发生。 这就是为什么没有用户分配了密钥保管库证书删除权，以及为什么没有公开要删除颁发者 CA 证书的 API 的原因。 安全删除证书颁发机构加密密钥的手动标准操作过程只能通过直接访问 Azure 门户中的密钥保管库来获取。 您还可以删除密钥保管库中的证书组。 为确保立即删除，请禁用[密钥保管库软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)功能。

## <a name="certificates"></a>证书

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>证书必须符合最低证书配置文件

OPC Vault 服务是向订阅者颁发最终实体证书的在线 CA。 OPC Vault 微服务在默认实现中遵循这些准则。

- 所有证书必须包含以下 X.509 字段，如下所述：
  - 版本字段的内容必须为 v3。 
  - 序列号字段的内容必须包括从 FIPS（联邦信息处理标准）140 经批准的随机数生成器获取的至少 8 个字节熵。<br>
    > [!IMPORTANT]
    > 默认情况下，OPC Vault 序列号为 20 字节，是从操作系统加密随机数生成器获取的。 随机数生成器是 FIPS 140 在 Windows 设备上批准，但不是在 Linux 上。 在选择使用 Linux VM 或 Linux Docker 容器的服务部署时，请考虑这一点，其中底层技术 OpenSSL 未批准 FIPS 140。
  - 颁发者唯一ID 和主题唯一 ID 字段不得存在。
  - 最终实体证书必须根据 IETF RFC 5280 使用基本约束扩展进行标识。
  - 对于颁发 CA 证书，必须将 pathLen 约束字段设置为 0。 
  - 扩展密钥使用扩展名必须存在，并且必须包含扩展密钥使用对象标识符 （OID） 的最小集。 不得指定任何扩展密钥使用 OID （2.5.29.37.0）。 
  - CRL 分发点 （CDP） 扩展必须存在于颁发者 CA 证书中。<br>
    > [!IMPORTANT]
    > CDP 扩展存在于 OPC 保管库 CA 证书中。 尽管如此，OPC UA 设备使用自定义方法来分发 CRL。
  - 授权信息访问扩展必须存在于订阅者证书中。<br>
    > [!IMPORTANT]
    > 颁发机构信息访问扩展位于 OPC Vault 订阅者证书中。 但是，OPC UA 设备使用自定义方法来分发颁发者 CA 信息。
- 必须使用经批准的非对称算法、密钥长度、哈希函数和填充模式。
  - RSA 和 SHA-2 是唯一受支持的算法。
  - RSA 可用于加密、密钥交换和签名。
  - RSA 加密必须仅使用 OAEP、RSA-KEM 或 RSA-PSS 填充模式。
  - 需要大于或等于 2048 位的钥匙长度。
  - 使用 SHA-2 系列哈希算法（SHA256、SHA384 和 SHA512）。
  - RSA 根 CA 密钥的典型生存期大于或等于 20 年，必须为 4096 位或更大。
  - RSA 颁发者 CA 密钥必须至少为 2048 位。 如果 CA 证书到期日期在 2030 年之后，CA 密钥必须为 4096 位或更大。
- 证书生存期
  - 根 CA 证书：根 CA 的最大证书有效期不得超过 25 年。
  - 子 CA 或联机颁发者 CA 证书：联机且仅颁发订阅者证书的 CA 的最大证书有效期不得超过 6 年。 对于这些 C，相关私有签名密钥的使用时间不得超过 3 年才能颁发新证书。<br>
    > [!IMPORTANT]
    > 颁发者证书，因为它是在默认的 OPC Vault 微服务中生成的，没有外部根 CA，被视为具有相应要求和生存期的联机子 CA。 默认生存期设置为 5 年，密钥长度大于或等于 2048。
  - 所有非对称密钥必须具有最长 5 年的生存期和建议的 1 年生存期。<br>
    > [!IMPORTANT]
    > 默认情况下，使用 OPC Vault 颁发的应用程序证书的生存期为 2 年，应每年更换。 
  - 每当续订证书时，都会使用新密钥续订证书。
- 应用程序实例证书中特定于 OPC UA 的扩展
  - 主题 AltName 扩展包括应用程序 Uri 和主机名。 这些地址可能包括 FQDN、IPv4 和 IPv6 地址。
  - 密钥使用包括数字签名、非再加密、密钥加密和数据加密。
  - 扩展的密钥使用包括服务器 Auth 和客户端 Auth。
  - 权威密钥标识符在签名的证书中指定。

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA 密钥和证书必须满足最低要求

- **私钥**：RSA 密钥必须至少为 2048 位。 如果 CA 证书到期日期在 2030 年之后，CA 密钥必须为 4096 位或更大。
- **生存期**：联机且仅颁发订阅者证书的 C 的最大证书有效期不得超过 6 年。 对于这些 C，相关私有签名密钥的使用时间不得超过 3 年才能颁发新证书。

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>使用硬件安全模块保护 CA 密钥

OpcVault 使用 Azure 密钥保管库高级版，密钥受 FIPS 140-2 级硬件安全模块 （HSM） 的保护。 

密钥保管库使用的加密模块（无论是 HSM 还是软件）均经过 FIPS 验证。 作为受 HSM 保护创建或导入的密钥在 HSM 中处理，验证为 FIPS 140-2 级 2。 作为软件保护创建或导入的密钥在加密模块中处理，这些模块验证为 FIPS 140-2 级 1。

## <a name="operational-practices"></a>业务实践

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>记录和维护证书注册的标准操作 PKI 实践

记录和维护标准操作过程 （SPS），用于 CA 如何颁发证书，包括： 
- 如何识别和验证订阅者。 
- 如何处理和验证证书请求（如果适用，还包括如何处理证书续订和重新密钥请求）。 
- 如何向订阅者分发颁发的证书。 

OPC Vault 微服务 SOP 在[OPC 保管库体系结构](overview-opc-vault-architecture.md)[和管理 OPC 保管库证书服务](howto-opc-vault-manage.md)中描述。 这些做法遵循"OPC 统一体系结构规范第 12 部分：发现和全球服务"。


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>记录和维护证书吊销的标准操作 PKI 做法

证书吊销过程在[OPC 保管库体系结构](overview-opc-vault-architecture.md)和管理[OPC 保管库证书服务](howto-opc-vault-manage.md)中进行了描述。
    
### <a name="document-ca-key-generation-ceremony"></a>文档 CA 密钥生成仪式 

由于 Azure 密钥保管库中的安全存储，OPC Vault 微服务中的颁发者 CA 密钥生成得到了简化。 有关详细信息，请参阅管理[OPC 保管库证书服务](howto-opc-vault-manage.md)。

但是，当您使用外部根证书颁发机构时，CA 密钥生成仪式必须遵守以下要求。

CA 密钥生成仪式必须针对至少包含以下项目的文档脚本执行： 
- 角色和参与者职责的定义。
- 批准 CA 密钥生成仪式的举行。
- 仪式所需的加密硬件和激活材料。
- 硬件准备（包括资产/配置信息更新和签核）。
- 操作系统安装。
- 在 CA 密钥生成仪式期间执行的特定步骤，例如： 
  - CA 应用程序安装和配置。
  - CA 密钥生成。
  - CA 密钥备份。
  - CA 证书签名。
  - 在服务的受保护 HSM 中导入已签名的密钥。
  - CA 系统关闭。
  - 准备储存材料。


## <a name="next-steps"></a>后续步骤

现在您已经学会了如何安全地管理 OPC Vault，您可以：

> [!div class="nextstepaction"]
> [使用 OPC 保管库保护 OPC UA 设备](howto-opc-vault-secure.md)
