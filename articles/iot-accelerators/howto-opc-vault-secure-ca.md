---
title: 如何安全运行 OPC 保管库证书管理服务-Azure |Microsoft Docs
description: 介绍如何安全地在 Azure 中运行 OPC 保管库证书管理服务，并回顾其他要考虑的安全准则。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271702"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>安全运行 OPC 保管库证书管理服务

本文介绍如何安全地在 Azure 中运行 OPC 保管库证书管理服务，并回顾其他要考虑的安全准则。

## <a name="roles"></a>角色

### <a name="trusted-and-authorized-roles"></a>可信角色和授权角色

OPC 保管库微服务允许不同角色访问服务的各个部分。

> [!IMPORTANT]
> 在部署过程中，脚本只会将运行部署脚本的用户添加为所有角色的用户。 对于生产部署，应查看此角色分配，并按照以下指南进行适当的重新配置。 此任务需要在 Azure Active Directory （Azure AD）企业应用程序门户中手动分配角色和服务。

### <a name="certificate-management-service-roles"></a>证书管理服务角色

OPC 保管库微服务定义下列角色：

- **读者**：默认情况下，租户中任何经过身份验证的用户都具有读取访问权限。 
  - 对应用程序和证书请求的读取访问权限。 可以列出和查询应用程序和证书请求。 此外，还可以使用读取访问权限访问设备发现信息和公共证书。
- **Writer**：向用户分配写入者角色，以便为某些任务添加写入权限。 
  - 应用程序和证书请求的读/写访问权限。 可以注册、更新和注销应用程序。 可以创建证书请求并获取已批准的私钥和证书。 还可以删除私钥。
- **审批者**：向用户分配审批者角色以批准或拒绝证书申请。 该角色不包括任何其他角色。
  - 除了访问 OPC 保管库微服务 API 的审批者角色外，用户还必须具有 Azure Key Vault 中的 "密钥签名" 权限，以便能够对证书进行签名。
  - 应将 "编写者" 和 "审批者" 角色分配给不同的用户。
  - 审批者的主要角色是批准证书请求的生成和拒绝。
- **管理员**：向用户分配管理员角色以管理证书组。 该角色不支持审批者角色，但包含 Writer 角色。
  - 管理员可以通过颁发新的证书吊销列表（CRL）来管理证书组、更改配置以及吊销应用程序证书。
  - 理想情况下，编写者、审批者和管理员角色分配给不同的用户。 为了进一步提高安全性，具有审批者或管理员角色的用户在 Key Vault 中还需要具有密钥签名权限，以颁发证书或续订颁发者 CA 证书。
  - 除了微服务管理角色以外，该角色还包括但不限于：
    - 管理 CA 安全做法的实现的责任。
    - 管理证书的生成、吊销和暂停。 
    - 加密密钥生命周期管理（例如，续订颁发者 CA 密钥）。
    - 操作 CA 的服务的安装、配置和维护。
    - 服务的日常操作。 
    - CA 和数据库的备份和恢复。

### <a name="other-role-assignments"></a>其他角色分配

当你运行该服务时，还应考虑以下角色：

- 具有外部根证书颁发机构的证书采购合同的业务所有者（例如，当所有者从外部 CA 购买证书或操作属于外部 CA 的 CA 时）。
- 证书颁发机构的开发和验证。
- 审查审核记录。
- 在*授权*角色中，帮助支持 ca 或管理物理和云设施，但不直接信任以执行 ca 操作的人员。 还必须记录授权角色中允许执行的任务组。

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>每季度查看受信任角色和授权角色的成员身份

至少按季度查看受信任角色和授权角色的成员身份。 确保每个角色中的一组人员（对于手动流程）或服务标识（对于自动流程）保持最小。

### <a name="role-separation-between-certificate-requester-and-approver"></a>证书请求者和审批者之间的角色分离

证书颁发过程必须在证书申请者和证书审批者角色（人员或自动化系统）之间强制实施角色分离。 证书颁发必须由证书审批者角色授权，该角色用于验证证书请求者是否有权获取证书。 持有证书审批者角色的人员必须是正式授权的人员。

### <a name="restrict-assignment-of-privileged-roles"></a>限制特权角色的分配

你应将特权角色的分配（例如，将管理员和审批者组的成员身份）限制为一组有限的授权人员。 任何特权角色更改都必须在24小时内撤销访问权限。 最后，按季度查看特权角色分配，并删除任何不需要或过期的分配。

### <a name="privileged-roles-should-use-two-factor-authentication"></a>特权角色应使用双因素身份验证

使用多重身份验证（也称为双因素身份验证），对服务的审批者和管理员进行交互式登录。

## <a name="certificate-service-operation-guidelines"></a>证书服务操作指南

### <a name="operational-contacts"></a>操作联系人

证书服务必须对文件具有最新的安全响应计划，其中包含详细的操作事件响应联系人。

### <a name="security-updates"></a>安全更新

所有系统都必须持续受到最新安全更新的监视和更新。

> [!IMPORTANT]
> OPC 保管库服务的 GitHub 存储库会持续更新安全修补程序。 监视这些更新，并定期将它们应用到服务。

### <a name="security-monitoring"></a>安全监视

订阅或实现适当的安全监视。 例如，订阅中心监视解决方案（例如 Azure 安全中心或 Office 365 监视解决方案），并对其进行适当的配置，以确保将安全事件传输到监视解决方案。

> [!IMPORTANT]
> 默认情况下，OPC 保管库服务将[Azure 应用程序 Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops)部署为监视解决方案。 强烈建议添加安全解决方案（例如[Azure 安全中心](https://azure.microsoft.com/services/security-center/)）。

### <a name="assess-the-security-of-open-source-software-components"></a>评估开源软件组件的安全性

产品或服务中使用的所有开源组件都必须没有中等或更大的安全漏洞。

> [!IMPORTANT]
> 在持续集成的生成过程中，OPC 保管库服务的 GitHub 存储库将扫描所有组件是否有漏洞。 在 GitHub 上监视这些更新，并定期将它们应用到服务。

### <a name="maintain-an-inventory"></a>维护库存

为所有生产主机（包括永久性虚拟机）、设备、所有内部 IP 地址范围、Vip 和公共 DNS 域名维护资产清单。 每次添加或删除系统、设备 IP 地址、VIP 或公共 DNS 域时，都必须在30天内更新清单。

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>默认 Azure OPC 保管库的清单微服务生产部署 

在 Azure 中：
- **应用服务计划**：服务主机的应用服务计划。 默认值为 S1。
- 微服务的**应用服务**： OPC 保管库服务主机。
- 应用**服务**示例应用程序： OPC 保管库示例应用程序主机。
- **Key Vault Standard**：用于存储 web 服务的机密和 Azure Cosmos DB 密钥。
- **Key Vault 高级**：承载颁发者 CA 密钥、签名服务以及保管库配置和应用程序私钥的存储。
- **Azure Cosmos DB**：针对应用程序和证书请求的数据库。 
- **Application Insights**：（可选） web 服务和应用程序的监视解决方案。
- **Azure AD 应用程序注册**：示例应用程序、服务和边缘模块的注册。

对于云服务，应记录用于部署服务的所有主机名、资源组、资源名称、订阅 Id 和租户 Id。 

在 Azure IoT Edge 或本地 IoT Edge 服务器：
- **OPC 保管库 IoT Edge 模块**：用于支持工厂网络 OPC UA 全局发现服务器。 

对于 IoT Edge 设备，应记录主机名和 IP 地址。 

### <a name="document-the-certification-authorities-cas"></a>记录证书颁发机构（Ca）

CA 层次结构文档必须包含所有操作的 Ca。 这包括所有相关的从属 Ca、父 Ca 和根 Ca，即使它们不受服务管理也是如此。 你可以不使用正式的文档，而是提供一组详尽的所有未过期的 CA 证书。

> [!NOTE]
> OPC 保管库示例应用程序支持下载服务中使用的所有证书，并在文档中生成。

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>记录所有证书颁发机构（Ca）颁发的证书

提供过去12个月颁发的所有证书的详尽集。

> [!NOTE]
> OPC 保管库示例应用程序支持下载服务中使用的所有证书，并在文档中生成。

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>记录安全删除加密密钥的标准操作过程

在 CA 的生存期内，密钥删除可能很少发生。 这就是用户未分配 Key Vault 证书删除权限的原因，并且没有公开用于删除颁发者 CA 证书的 Api 的原因。 安全删除证书颁发机构加密密钥的手动标准操作过程仅通过直接访问 Azure 门户中的 Key Vault 可用。 你还可以在 Key Vault 中删除证书组。 若要确保立即删除，请禁用[Key Vault 软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)功能。

## <a name="certificates"></a>证书

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>证书必须符合最低证书配置文件

OPC 保管库服务是向订户颁发最终实体证书的联机 CA。 OPC 保管库微服务在默认实现中遵循这些准则。

- 所有证书都必须包含以下 x.509 字段，如下所示：
  - 版本字段的内容必须是 v3。 
  - SerialNumber 字段的内容必须包括从 FIPS （联邦信息处理标准）140批准的随机数生成器获取的至少8个字节的熵。<br>
    > [!IMPORTANT]
    > 默认情况下，OPC 保管库序列号为20个字节，并且是从操作系统加密随机数生成器获取的。 随机数生成器是在 Windows 设备上批准的 FIPS 140，而不是在 Linux 上。 选择使用 Linux Vm 或 Linux docker 容器的服务部署时，请考虑这一点，而基础技术 OpenSSL 不会获得 FIPS 140 的批准。
  - IssuerUniqueID 和 subjectUniqueID 字段不得存在。
  - 最终实体证书必须根据 IETF RFC 5280 在基本约束扩展中标识。
  - 对于发证 CA 证书，pathLenConstraint 字段必须设置为0。 
  - 扩展密钥用法扩展必须存在，并且必须包含扩展密钥用法对象标识符（Oid）的最小集合。 不得指定 anyExtendedKeyUsage OID （2.5.29.37.0）。 
  - CRL 分发点（CDP）扩展必须存在于颁发者 CA 证书中。<br>
    > [!IMPORTANT]
    > OPC 保管库 CA 证书中存在 CDP 扩展。 尽管如此，OPC UA 设备使用自定义方法来分发 Crl。
  - 颁发机构信息访问扩展必须存在于订阅者证书中。<br>
    > [!IMPORTANT]
    > OPC 保管库订阅者证书中存在授权信息访问扩展。 尽管如此，OPC UA 设备使用自定义方法来分发颁发者的 CA 信息。
- 必须使用批准的非对称算法、密钥长度、哈希函数和填充模式。
  - RSA 和 SHA-1 是唯一受支持的算法。
  - RSA 可用于加密、密钥交换和签名。
  - RSA 加密必须仅使用 OAEP、RSA-KEM 或 RSA-PSS 填充模式。
  - 需要大于或等于2048位的密钥长度。
  - 使用 SHA-1 系列哈希算法（SHA256、SHA384 和 SHA512）。
  - 对于典型生存期大于或等于20年的 RSA 根 CA 密钥，必须为4096位或更高。
  - RSA 颁发者 CA 密钥必须至少为2048位。 如果 CA 证书到期日期在2030之后，则 CA 密钥必须是4096或更高版本。
- 证书生存期
  - 根 CA 证书：根 Ca 的最大证书有效期不能超过25年。
  - 子 CA 或联机颁发者 CA 证书：处于联机状态且仅颁发订户证书的 Ca 的最大证书有效期不能超过6年。 对于这些 Ca，相关的专用签名密钥的使用时间不得超过3年，无法颁发新证书。<br>
    > [!IMPORTANT]
    > 如果颁发者证书是在默认 OPC 保管库微服务中生成的，而不是外部根 CA，则会将其视为具有相应要求和生存期的联机子 CA。 默认生存期设置为5年，密钥长度大于或等于2048。
  - 所有非对称密钥必须具有最长5年的生存期和建议的1年生命周期。<br>
    > [!IMPORTANT]
    > 默认情况下，使用 OPC 保管库颁发的应用程序证书的生存期的生存期为2年，应每年替换一次。 
  - 续订证书时，会使用新密钥续订证书。
- 应用程序实例证书中的 OPC UA 特定扩展
  - SubjectAltName 扩展插件包括应用程序 Uri 和主机名。 它们还可能包括 FQDN、IPv4 和 IPv6 地址。
  - 密钥用法包括 digitalSignature、认可、keyEncipherment 和 dataEncipherment。
  - ExtendedKeyUsage 包括 serverAuth 和 clientAuth。
  - AuthorityKeyIdentifier 是在已签名的证书中指定的。

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA 密钥和证书必须满足最低要求

- **私钥**： RSA 密钥必须至少为2048位。 如果 CA 证书到期日期在2030之后，则 CA 密钥必须是4096或更高版本。
- **生存期**：处于联机状态且仅颁发订户证书的 ca 的最大证书有效期不能超过6年。 对于这些 Ca，相关的专用签名密钥的使用时间不得超过3年，无法颁发新证书。

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>使用硬件安全模块保护 CA 密钥

OpcVault 使用 Azure Key Vault Premium，密钥受 FIPS 140-2 级别2硬件安全模块（HSM）的保护。 

Key Vault 使用的加密模块（使用 HSM 或软件）是否验证了 FIPS。 创建或导入为 HSM 保护的密钥在 HSM 内进行处理，验证为 FIPS 140-2 级别2。 在验证为 FIPS 140-2 级别1的加密模块内处理密钥被创建或导入为软件保护的密钥。

## <a name="operational-practices"></a>操作做法

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>为证书注册记录和维护标准操作 PKI 实践

记录和维护 Ca 颁发证书的方式的标准操作过程（Sop），包括： 
- 如何标识和验证订阅服务器。 
- 如何处理和验证证书请求（如果适用，还包括如何处理证书续订和 rekey 性请求）。 
- 如何将颁发的证书分发到订阅服务器。 

Opc 保管库[体系结构](overview-opc-vault-architecture.md)和[管理 opc 保管库证书服务](howto-opc-vault-manage.md)中介绍了 opc 保管库微服务。 此做法遵循 "OPC 统一体系结构规范第12部分：发现和全局服务"。


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>记录和维护证书吊销的标准操作 PKI 实践

证书吊销过程在[Opc 保管库体系结构](overview-opc-vault-architecture.md)和[管理 opc 保管库证书服务](howto-opc-vault-manage.md)中进行了介绍。
    
### <a name="document-ca-key-generation-ceremony"></a>文档 CA 密钥生成仪式 

由于 Azure Key Vault 中的安全存储，OPC 保管库微服务中的颁发者 CA 密钥生成被简化。 有关详细信息，请参阅[管理 OPC 保管库证书服务](howto-opc-vault-manage.md)。

但是，在使用外部根证书颁发机构时，CA 密钥生成仪式必须符合以下要求。

必须对至少包含以下各项的已记录脚本执行 CA 密钥生成操作： 
- 角色和参与者职责的定义。
- 批准执行 CA 密钥生成仪式。
- 工作所需的加密硬件和激活材料。
- 硬件准备（包括资产/配置信息更新和注销）。
- 操作系统安装。
- CA 密钥生成过程中执行的特定步骤，如： 
  - CA 应用程序的安装和配置。
  - CA 密钥生成。
  - CA 密钥备份。
  - CA 证书签名。
  - 导入服务的受保护 HSM 中的签名密钥。
  - CA 系统关闭。
  - 准备存储的资料。


## <a name="next-steps"></a>后续步骤

现在，你已了解如何安全管理 OPC 保管库，你可以：

> [!div class="nextstepaction"]
> [保护 OPC UA 设备与 OPC 保管库](howto-opc-vault-secure.md)
