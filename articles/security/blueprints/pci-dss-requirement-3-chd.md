---
title: Azure 付款处理蓝图 - CHD 要求
description: PCI DSS 要求 3
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 7ff8a412b16025afe2640b73b51d9b2a2fdca354
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895444"
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容环境的 CHD 要求
## <a name="pci-dss-requirement-3"></a>PCI DSS 要求 3

**保护存储的持卡人数据**

> [!NOTE]
> 这些要求由[支付卡行业 (PCI) 安全标准委员会](https://www.pcisecuritystandards.org/pci_security/)定义，作为 [PCI 数据安全标准 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)的一部分。 请参阅 PCI DSS，了解每项要求的测试过程和指南。

保护方法（例如加密、截断、屏蔽、哈希）是持卡人数据保护的关键组件。 如果某个入侵者绕过了其他安全控制，获得了加密数据的访问权限，但在没有正确加密密钥的情况下，这些数据对该人来说是无法读取和使用的。 还应考虑使用其他有效的保护已存储数据的方法来缓解风险。 例如，在使用相关方法尽量降低风险时，除非绝对必要，否则不存储持卡人数据；如果不需要完整的 PAN，则截断持卡人数据；不使用最终用户消息传送技术（例如电子邮件和即时消息）来发送不受保护的 PAN。
请参阅 PCI DSS 和 PA-DSS 术语表、缩写和首字母缩写，了解“强加密”和其他 PCI DSS 术语的定义。

## <a name="pci-dss-requirement-31"></a>PCI DSS 要求 3.1

**3.1** 尽量减少持卡人数据存储，方法是实施数据保留和处置策略、过程和流程，其中至少包括以下针对所有持卡人数据 (CHD) 存储的要求：
- 在符合法律、法规和业务要求的情况下，尽量降低数据存储量和缩短保留时间
- 针对持卡人数据制定特定的保留要求
- 制定安全删除不再需要的数据的流程
- 按季确定存储的超过所定义保留期的持卡人数据并将其安全地删除。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Azure 有责任确保指定要删除的客户数据已使用在安全处置策略中指定的 NIST 800-88 兼容协议安全地取消配置。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 不删除或销毁任何存储的 CHD。 但是，所有数据都会加密，不存储任何主帐号 (PAN) 数据。<br /><br />|



## <a name="pci-dss-requirement-32"></a>PCI DSS 要求 3.2

**3.2** 不在授权后存储敏感的身份验证数据（即使是在加密的情况下）。 如果收到敏感的身份验证数据，则应在完成授权过程后实施安全措施，使得所有数据无法恢复。 
- 存在业务理由，且 
- 数据的存储方式安全。
敏感身份验证数据包括下面的要求 3.2.1 到 3.2.3 中提到的数据：


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 不删除或销毁任何存储的 CHD：存储示例数据只是为了进行演示。 但是，所有数据都会加密，不存储任何主帐号 (PAN) 数据。<br /><br />|



### <a name="pci-dss-requirement-321"></a>PCI DSS 要求 3.2.1

**3.2.1** 不在授权后存储任何磁道的完整内容（这些内容来自卡背面的磁条，芯片上或其他位置包含等效数据）。 此类数据也称为完整磁道、磁道、磁道 1、磁道 2、磁条数据。 

> [!NOTE]
> 在正常业务过程中，磁条中的以下数据元素可能需要保留： 
> - 持卡人的姓名 
> - 主帐号 (PAN) 
> - 到期日期 
> - 服务代码 
>
> 为了尽量降低风险，只在业务需要的情况下存储这些数据元素。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 不存储任何 CHD 的完整内容。|



### <a name="pci-dss-requirement-322"></a>PCI DSS 要求 3.2.2

**3.2.2** 不在授权后存储卡验证代码或值（支付卡前面或背面印刷的三位或四位数字，用于验证无卡支付）。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 加密所有数据，包括 CVV 示例。|



### <a name="pci-dss-requirement-323"></a>PCI DSS 要求 3.2.3

**3.2.3** 不在授权后存储个人标识号 (PIN) 或加密的 PIN 块。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 不存储 PIN 信息。|



## <a name="pci-dss-requirement-33"></a>PCI DSS 要求 3.3

**3.3** 在显示时屏蔽 PAN（最多显示头六个和后四个数字），只允许存在合法业务需要的人员查看完整 PAN。 

> [!NOTE]
> 此要求不替代更严格的针对持卡人数据显示的已实施要求—例如，针对销售点 (POS) 回执的法律或支付卡品牌要求。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用透明数据加密、Always Encrypted 列和动态数据掩码来屏蔽主帐号 (PAN)。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



## <a name="pci-dss-requirement-34"></a>PCI DSS 要求 3.4

**3.4** 使用下述任意方法，使得 PAN 在存储位置（包括可移植数字媒体、备份媒体和日志）不可读：
- 基于强加密的单向哈希（必须是整个 PAN 的哈希）
- 截断（哈希不能用于替换截断的 PAN 段）
- 索引令牌和填充（填充必须安全地进行存储）
- 使用关联的密钥管理流程和过程进行的强加密。 

> [!NOTE]
> 如果能够访问截断的和哈希的 PAN 版本，恶意攻击者可以不太费力地重新构造原始 PAN 数据。 如果同一 PAN 的哈希版和截断版存在于一个实体的环境中，则必须实施其他控制，确保不能通过将哈希版和截断版关联在一起来重新构造原始 PAN。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 加密所有信用卡数据，并使用 Azure Key Vault 管理密钥，防止检索 CHD。<br /><br />有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-341"></a>PCI DSS 要求 3.4.1

**3.4.1** 如果使用磁盘加密（而不是文件或列级别的数据库加密），则必须单独管理逻辑访问，使之独立于本机操作系统身份验证和访问控制机制（例如，不使用本地用户帐户数据库或通用网络登录凭据）。 加密密钥不得与用户帐户相关联。 

> [!NOTE]
> 此要求在所有其他 PCI DSS 加密和密钥管理要求的基础上应用。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 加密所有存储的数据，并将流量分开，防止针对 DevOps 功能的特权提升。<br /><br />对应用服务环境进行保护和锁定以后，需实施一项机制来允许任何可能是必需的 DevOps 版本或更改，例如使用 Kudu 监视 Web 应用的功能。<br /><br />可以使用以下配置将虚拟机设置为 jumpbox（守护主机）：<br /><br /><ul><li>[反恶意软件扩展](/azure/security/azure-security-antimalware)</li><li>[Log Analytics 监视扩展](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[VM 诊断扩展](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[Bitlocker 加密磁盘](/azure/security/azure-security-disk-encryption)</li></ul>使用 Azure Key Vault 符合 Azure 政府、PCI DSS 和 HIPAA 要求。|



## <a name="pci-dss-requirement-35"></a>PCI DSS 要求 3.5

**3.5** 记录和实施所使用的密钥保护过程，防止存储的持卡人数据泄漏和滥用。 

> [!NOTE]
> 此要求适用于加密存储的持卡人数据时所使用的密钥，也适用于保护数据加密密钥时所使用的密钥加密密钥—此类密钥加密密钥的强度必须至少与数据加密密钥的强度一样。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | **对于使用 Key Vault 的客户：**<br /><br />Microsoft Azure 确保客户密钥保管库在逻辑上彼此隔离，以及在逻辑上与 Key Vault 服务的管理平面隔离。 Key Vault 的设计使得 Microsoft 也没有客户密钥保管库的访问权限。 <br /><br />密钥由 Microsoft Azure 使用行业标准算法、密钥长度和硬件安全模块 (HSM) 进行保护。<br /><br />在 Microsoft Azure Key Vault 中存储的密钥可以用来保护另一密钥。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 可以利用 Contoso Webstore 提供的演示和帮助文档来部署受保护密钥解决方案，从而保护演示 CHD。|



### <a name="pci-dss-requirement-351"></a>PCI DSS 要求 3.5.1

**3.5.1** 仅适用于服务提供商的其他要求：始终记录加密体系结构的说明，其中包括：
- 所有用于保护持卡人数据的算法、协议和密钥的详细信息，包括密钥强度和过期日期
- 每个密钥的密钥用法说明
- 一个清单，其中包含任何 HSM 和其他用于密钥管理的 SCD 

> [!NOTE]
> 此要求在 2018 年 1 月 31 日之前为最佳做法，该日期之后才成为要求。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | **对于使用 Key Vault 的客户：**<br /><br />Microsoft Azure 确保客户密钥保管库在逻辑上彼此隔离，以及在逻辑上与 Key Vault 服务的管理平面隔离。 Key Vault 的设计使得 Microsoft 也没有客户密钥保管库的访问权限。 <br /><br />密钥由 Microsoft Azure 使用行业标准算法、密钥长度和硬件安全模块 (HSM) 进行保护。<br /><br />在 Microsoft Azure Key Vault 中存储的密钥可以用来保护另一密钥。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 可以利用 Contoso Webstore 提供的演示和帮助文档来部署受保护密钥解决方案，从而保护演示 CHD。|



### <a name="pci-dss-requirement-352"></a>PCI DSS 要求 3.5.2

**3.5.2** 仅限最少数量的必要管理员访问加密密钥。


**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | **对于使用 Key Vault 的客户：**<br /><br />Key Vault 支持细化的访问策略，允许 Key Vault 所有者授予特定功能的访问权限，对特定实体执行特定操作。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 密钥管理只能使用一个用户帐户 (admin##@contosowebstore.com)。|



### <a name="pci-dss-requirement-353"></a>PCI DSS 要求 3.5.3

**3.5.3** 始终使用下面的一种（或多种）格式存储用来加密/解密持卡人数据的机密和私钥：
- 使用密钥加密密钥来加密，该密钥在强度上至少与数据加密密钥一样，与数据加密密钥分开存储
- 位于安全的加密设备（例如硬件（主机）安全模块 (HSM) 或 PTS 审批的交互点设备）内
- 充当至少两个完整的密钥组件或密钥共享，采用行业接受的方法 

> [!NOTE]
> 公钥不需使用上述格式之一来存储。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | **对于使用 Key Vault 的客户：**<br /><br />密钥存储在客户标识的特定密钥保管库中。<br /><br />Key Vault 允许多个应用程序对其同时进行全局访问，这样就减少了复制密钥并将其存储在多个位置的需求。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Key Vault 进行所有密钥管理。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-354"></a>PCI DSS 要求 3.5.4

**3.5.4** 将加密密钥存储在尽可能少的位置。


**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | **对于使用 Key Vault 的客户：**<br /><br />密钥存储在客户标识的特定密钥保管库中。 <br /><br />Key Vault 允许多个应用程序对其同时进行全局访问，这样就减少了复制密钥并将其存储在多个位置的需求。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Key Vault 进行所有密钥管理。有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



## <a name="pci-dss-requirement-36"></a>PCI DSS 要求 3.6

**3.6** 对于用于加密持卡人数据的加密密钥，完整地记录和实施包括以下内容在内的所有密钥管理流程和过程。 

> [!NOTE]
> 密钥管理的许多行业标准在各种资源（包括 NIST，可在 http://csrc.nist.gov 中找到）中提供。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Key Vault 进行所有密钥管理。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-361"></a>PCI DSS 要求 3.6.1

**3.6.1** 生成强加密密钥

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | **对于使用 Key Vault 的客户：** <br /><br />在 Key Vault 中生成密钥时，Azure 负责根据客户的规范生成密钥。 密钥使用 HSM 生成。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Key Vault 进行所有密钥管理。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-362"></a>PCI DSS 要求 3.6.2

**3.6.2** 确保加密密钥分发安全

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | **对于使用 Key Vault 的客户：**<br /><br />自带密钥 (BYOK) 工具可封装客户密钥，并以绑定到特定 Azure 订阅的特定安全保管库为目标。 只能将密钥导入到已定义订阅的密钥保管库，后者位于指定的区域。 此流程使用硬件制造商规定的加密过程。 客户会收到传输成功的通知。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Key Vault 进行所有密钥管理。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-363"></a>PCI DSS 要求 3.6.3

**3.6.3** 确保加密密钥存储安全

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | **对于使用 Key Vault 的客户：**<br /><br />密钥存储在 HSM 中，使用硬件制造商的加密安全措施进行保护。 密钥的元数据以加密状态存储在 Azure 存储中，该状态特定于每个密钥保管库。 <br /><br /> |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Key Vault 进行所有密钥管理。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-364"></a>PCI DSS 要求 3.6.4

**3.6.4** 在密钥的加密期终止以后（例如，已经过了定义的时限，而且/或者给定的密钥已生成特定大小的密码文本），加密密钥会更改。该加密期由关联的应用程序供应商或密钥所有者根据行业最佳做法和准则（例如 NIST 特别发布版 800-57）来定义。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | **对于使用 Key Vault 的客户：**<br /><br />Key Vault 支持客户定义的更新或轮替密钥的功能。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Key Vault 进行所有密钥管理。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-365"></a>PCI DSS 要求 3.6.5

**3.6.5** 当密钥的完整性受损（例如，了解明文密钥组件的员工离职）或者怀疑密钥泄露时，需停用或替换（例如，存档、析构和/或吊销）密钥。 

> [!NOTE]
> 如果需要保留已停用或替换的加密密钥，必须将这些密钥安全地进行存档（例如，使用密钥加密密钥来存档）。 存档的加密密钥只能用于解密/验证目的。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | **对于使用 Key Vault 的客户：**<br /><br />Key Vault 支持客户定义的停用或替换功能。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Key Vault 进行所有密钥管理。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-366"></a>PCI DSS 要求 3.6.6

**3.6.6** 如果使用手动的明文加密密钥管理操作，则必须通过分开掌握和双重控制的手段来管理这些操作。 

> [!NOTE]
> 例如，手动密钥管理操作包括但不限于：密钥的生成、传输、加载、存储、析构。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Key Vault 进行所有密钥管理。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-367"></a>PCI DSS 要求 3.6.7

**3.6.7** 防止未经授权就替换加密密钥。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | **对于使用 Key Vault 的客户：**<br /><br />Key Vault 在逻辑上是隔离的，不支持跨目录授权， 因此可以阻止非授权替换。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Key Vault 进行所有密钥管理。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-368"></a>PCI DSS 要求 3.6.8

**3.6.8** 要求加密密钥管理员正式确认自己了解并接受密钥管理员责任。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 密钥管理只能使用一个用户帐户 (admin##@contosowebstore.com)。|



## <a name="pci-dss-requirement-37"></a>PCI DSS 要求 3.7

**3.7** 确保记录在保护存储的持卡人数据时使用过哪些安全策略和操作过程，并将其告知受影响的各方。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Key Vault 进行所有密钥管理。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|




