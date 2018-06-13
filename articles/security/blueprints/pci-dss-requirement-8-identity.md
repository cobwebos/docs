---
title: Azure 付款处理蓝图 - 标识要求
description: PCI DSS 要求 8
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a83040a6b5174307ea73e5473165835458d217f6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894895"
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容环境的标识要求 
## <a name="pci-dss-requirement-8"></a>PCI DSS 要求 8

**标识并验证对系统组件的访问**

> [!NOTE]
> 这些要求由[支付卡行业 (PCI) 安全标准委员会](https://www.pcisecuritystandards.org/pci_security/)定义，作为 [PCI 数据安全标准 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)的一部分。 请参阅 PCI DSS，了解每项要求的测试过程和指南。

为每位具有访问权限的人员分配唯一标识 (ID)，确保每个人都能对其操作负责。 建立此类问责制度以后，即可允许已知的授权用户和流程对关键数据和系统执行操作并对该操作进行跟踪。
密码的有效性大部分取决于身份验证系统的设计和实施。具体说来就是，允许攻击者进行密码尝试的频率如何？使用了哪些安全方法在密码输入、传输和存储过程中对用户密码进行保护？

> [!NOTE]
> 这些要求适用于所有帐户，包括具有管理功能的销售点帐户，以及用于查看和访问持卡人数据或持卡人数据所在系统的所有帐户。 这包括供应商和其他第三方使用的帐户（例如，用于支持或维护的帐户）。 这些要求不适用于消费者（例如持卡人）使用的帐户。 不过，要求 8.1.1、8.2、8.5、8.2.3 到 8.2.5、8.1.6 到 8.1.8 本来就不适用于销售点付款应用程序中的用户帐户，此类帐户（例如收银员帐户）一次只能访问一个卡号，目的是方便单次交易。
 
## <a name="pci-dss-requirement-81"></a>PCI DSS 要求 8.1

**8.1** 定义和实施策略和过程，确保在所有系统组件上为非消费者用户和管理员进行适当的用户识别管理，如下所示。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 对于如何正确使用管理员进行示例部署，Contoso Webstore 提供了一个用例和说明。|



### <a name="pci-dss-requirement-811"></a>PCI DSS 要求 8.1.1

**8.1.1** 为所有用户分配一个唯一 ID，然后再让其访问系统组件或持卡人数据。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 实施 Azure Active Directory 和基于 Azure Active Directory 角色的访问控制 (RBAC)，确保所有用户都有唯一 ID。 有关详细信息，请参阅 [PCI 指南 - 标识管理](payment-processing-blueprint.md#identity-management)。<br /><br />|



### <a name="pci-dss-requirement-812"></a>PCI DSS 要求 8.1.2

**8.1.2** 控制用户 ID、凭据和其他标识符对象的添加、删除和修改。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 实施 Azure Active Directory 和基于 Azure Active Directory 角色的访问控制 (RBAC)，确保所有用户都有唯一 ID。 有关详细信息，请参阅 [PCI 指南 - 标识管理](payment-processing-blueprint.md#identity-management)。<br /><br />|



### <a name="pci-dss-requirement-813"></a>PCI DSS 要求 8.1.3

**8.1.3** 立即撤销任何已终止用户的访问权限。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Active Directory 进行用户管理。 可以在 Active Directory 中吊销用户。|



### <a name="pci-dss-requirement-814"></a>PCI DSS 要求 8.1.4

**8.1.4** 删除或禁用处于不活跃状态（90 天内）的用户帐户。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Active Directory 进行用户管理。 可对 `-enableADDomainPasswordPolicy` 选项进行设置，确保密码在 90 天内过期。|



### <a name="pci-dss-requirement-815"></a>PCI DSS 要求 8.1.5

**8.1.5** 对第三方用来通过远程访问进行系统组件访问、支持或维护的 ID 进行管理，如下所示：
- 仅在需要的时段内启用，在不使用时禁用。
- 在使用时进行监视。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 已采用适用的公司和组织安全策略，包括信息安全策略。 这些策略已经批准、发布并传送到 Microsoft Azure。 信息安全策略要求基于业务理由通过资产所有者授权授予对 Microsoft Azure 资产的访问权限，并基于“需要知道”和“最小特权”原则对访问权限进行限制。 该策略还满足访问管理生命周期的要求，包括访问权限预配、身份验证、访问授权、删除访问权限、以及定期进行访问审核。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 演示版已实施 Azure Active Directory 和基于 Azure Active Directory 角色的访问控制，目的是管理用户对安装内容的访问。 有关详细信息，请参阅 [PCI 指南 - 标识管理](payment-processing-blueprint.md#identity-management)。<br /><br />|



### <a name="pci-dss-requirement-816"></a>PCI DSS 要求 8.1.6

**8.1.6** 限制反复访问尝试，方法是在多次尝试（不超过六次）后锁定用户 ID。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 对所有演示版用户实施明确的职责划分 (SOD)。 有关详细信息，请参阅 [PCI 指南 - 标识管理](payment-processing-blueprint.md#identity-management)中的“Azure Active Directory Identity Protection”。|



### <a name="pci-dss-requirement-817"></a>PCI DSS 要求 8.1.7

**8.1.7** 将锁定时间设置为至少 30 分钟，或者设置为一直锁定到管理员启用该用户 ID。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责创建、强制和监视符合 PCI DSS 要求的密码策略。|



### <a name="pci-dss-requirement-818"></a>PCI DSS 要求 8.1.8

**8.1.8** 如果某个会话处于空闲状态的时间超过 15 分钟，则要求用户重新进行身份验证，以便重新激活终端或会话。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责创建、强制和监视符合 PCI DSS 要求的密码策略。|



## <a name="pci-dss-requirement-82"></a>PCI DSS 要求 8.2

**8.2** 除了分配唯一 ID，还请确保在所有系统组件上为非消费者用户和管理员进行适当的用户身份验证管理，方法是采用下述方法中的至少一种对所有用户进行身份验证：
- 你知道的东西，例如密码或通行短语
- 你拥有的东西，例如令牌设备或智能卡
- 你的特征，例如生物识别

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 已禁用 Contoso Webstore 实施多重身份验证，目的是方便用户使用演示版。 可以使用 [Azure 多重身份验证](https://azure.microsoft.com/services/multi-factor-authentication/)来实施多重身份验证。|



### <a name="pci-dss-requirement-821"></a>PCI DSS 要求 8.2.1

**8.2.1** 通过强加密使所有系统组件上的所有身份验证凭据（例如密码/通行短语）在传输和存储过程中不可读。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 已建立密钥管理过程，在整个生命周期（例如，生成、分发、吊销）中管理加密密钥。 Microsoft Azure 使用 Microsoft 的公司 PKI 基础结构。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 根据部署指南中的记述强制实施强密码。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。<br /><br />|



### <a name="pci-dss-requirement-822"></a>PCI DSS 要求 8.2.2

**8.2.2** 在修改任何身份验证凭据（例如，执行密码重置、预配新令牌或生成新密钥）之前验证用户标识。


**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 已建立密钥管理过程，在整个生命周期（例如，生成、分发、吊销）中管理加密密钥。 Microsoft Azure 使用 Microsoft 的公司 PKI 基础结构。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 根据部署指南中的记述强制实施强密码。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-823"></a>PCI DSS 要求 8.2.3

**8.2.3** 密码/通行短语必须符合以下要求：
- 至少需要七个字符长。
- 包含数字和字母字符。
或者，密码/通行短语的复杂性和强度必须至少与上述参数等效。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 根据部署指南中的记述强制实施强密码。|



### <a name="pci-dss-requirement-824"></a>PCI DSS 要求 8.2.4

**8.2.4** 至少每 90 天更改用户密码/通行短语一次。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Active Directory 进行用户管理。 可对 `-enableADDomainPasswordPolicy` 选项进行设置，确保密码每 90 天至少过期一次。|



### <a name="pci-dss-requirement-825"></a>PCI DSS 要求 8.2.5

**8.2.5** 不允许用户提交的新密码/通行短语与其刚使用过的四个密码/通行短语中的任何 一个相同。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 根据部署指南中的记述强制实施强密码。 有关详细信息，请参阅 [PCI 指南 - 标识管理](payment-processing-blueprint.md#identity-management)。<br /><br />|



### <a name="pci-dss-requirement-826"></a>PCI DSS 要求 8.2.6

**8.2.6** 将每个用户第一次使用和重置的密码/通行短语设置为唯一值，并在第一次使用后立即更改。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 根据部署指南中的记述强制实施强密码。 有关详细信息，请参阅 [PCI 指南 - 标识管理](payment-processing-blueprint.md#identity-management)。<br /><br />|



## <a name="pci-dss-requirement-83"></a>PCI DSS 要求 8.3

**8.3** 使用多重身份验证，确保对持卡人数据环境 (CDE) 进行的所有个人非控制台管理访问和所有远程访问的安全性。

> [!NOTE]
> 多重身份验证要求至少使用三种身份验证方法（请参阅“要求 8.2”，了解对身份验证方法的说明）中的两种进行身份验证。 不应将使用单重身份验证两次（例如，使用两个不同的密码）视为多重身份验证。


**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Azure 管理员在对 Azure 系统和服务器进行维护和管理时，必须使用多重身份验证进行访问。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 在部署期间创建三个帐户：admin、sqladmin 和 edna（执行演示期间登录到 Web 应用的默认用户）。 不对演示版实施多重身份验证。|



### <a name="pci-dss-requirement-831"></a>PCI DSS 要求 8.3.1

**8.3.1** 对于具有管理访问权限的人员，其对 CDE 进行的所有非控制台访问都实施多重身份验证。

> [!NOTE]
> 此要求在 2018 年 1 月 31 日之前为最佳做法，该日期之后才成为要求。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Azure 管理员在对 Azure 系统和服务器进行维护和管理时，必须使用多重身份验证进行访问。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 在部署期间创建三个帐户：admin、sqladmin 和 edna（执行演示期间登录到 Web 应用的默认用户）。 不对演示版实施多重身份验证。|



### <a name="pci-dss-requirement-832"></a>PCI DSS 要求 8.3.2

**8.3.2** 对源自实体网络外部的所有远程网络访问（适用于用户和管理员，包括进行支持或维护操作的第三方访问）实施多重身份验证。


**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Azure 管理员在对 Azure 系统和服务器进行维护和管理时，必须使用多重身份验证进行访问。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 在部署期间创建三个帐户：admin、sqladmin 和 edna（执行演示期间登录到 Web 应用的默认用户）。 不对演示版实施多重身份验证。|



## <a name="pci-dss-requirement-84"></a>PCI DSS 要求 8.4

**8.4** 记录下述身份验证策略和过程并将其传达给所有用户：
- 强身份验证凭据选择指南
- 用户身份验证凭据保护指南
- 关于不重复使用以前使用过的密码的说明
- 在怀疑密码泄漏的情况下更改密码的说明

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户有责任遵循相关指南以及记录身份验证过程和策略，并将其传达给所有用户。|



## <a name="pci-dss-requirement-85"></a>PCI DSS 要求 8.5

**8.5** 请勿使用组、共享或泛型 ID、密码或其他身份验证方法，如下所示：
- 泛型用户 ID 会被禁用或删除。
- 不得将共享用户 ID 用于系统管理和其他关键功能。
- 不得将共享和泛型用户 ID 用于管理任何系统组件。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 在部署期间创建三个帐户：admin、sqladmin 和 edna（执行演示期间登录到 Web 应用的默认用户）。 不对演示版实施多重身份验证。|



### <a name="pci-dss-requirement-851"></a>PCI DSS 要求 8.5.1

**8.5.1** **仅适用于服务提供商的其他要求：** 可以远程访问客户端进行相关操作（例如，对 POS 系统或服务器提供支持）的服务提供商必须对每个客户使用唯一身份验证凭据（例如密码/通行短语）。 

> [!NOTE]
> 此要求不适用于共享托管提供商访问其自己的托管环境，此类环境中会托管多个客户环境。

**责任：&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用于 Microsoft Azure 客户。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用于 Microsoft Azure 客户。|



## <a name="pci-dss-requirement-86"></a>PCI DSS 要求 8.6

**8.6** 在使用其他身份验证机制（例如，物理或逻辑安全令牌、智能卡、证书等）的情况下，使用这些机制必须遵循以下要求：
- 身份验证机制必须指定给单个帐户，不得在多个帐户之间共享。
- 物理和/或逻辑控制必须到位，确保只有预期的帐户可以使用该机制获得访问权限。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 在部署期间创建三个帐户：admin、sqladmin 和 edna（执行演示期间登录到 Web 应用的默认用户）。 不对演示版实施多重身份验证。 所有访问都通过 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 进行管理，后者有助于保护云应用程序和服务使用的加密密钥和机密。 |



## <a name="pci-dss-requirement-87"></a>PCI DSS 要求 8.7

**8.7** 只要是对包含持卡人数据的数据库进行的访问（包括由应用程序、管理员和所有其他用户进行的访问），都必须遵循以下要求：
- 对数据库进行的所有用户访问、用户查询和用户操作都通过编程方式进行。
- 只允许数据库管理员直接访问或查询数据库。
- 数据库应用程序的应用程序 ID 只能由应用程序使用（不得由个人用户或其他非应用程序进程使用）。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 通过 Azure Key Vault 保护所有持卡人数据，而记录加密则在部署中有概述。 有关详细信息，请参阅 [PCI 指南 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。<br /><br />|



## <a name="pci-dss-requirement-88"></a>PCI DSS 要求 8.8

**8.8** 确保记录在进行标识和身份验证时使用过哪些安全策略和操作过程，并将其告知受影响的各方。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责确保记录在进行标识和身份验证时使用过哪些安全策略和操作过程，并将其告知受影响的各方。|




