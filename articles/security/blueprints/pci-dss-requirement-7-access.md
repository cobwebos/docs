---
title: "Azure 付款处理蓝图 - 访问要求"
description: "PCI DSS 要求 7"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5a3c9eac552fb96309cfa791a2e72a7102662e60
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>符合 PCI DSS 的环境的访问要求 
## <a name="pci-dss-requirement-7"></a>PCI DSS 要求 7

**按需要知道的业务限制对持卡人数据的访问**

> [!NOTE]
> 这些要求由[支付卡行业 (PCI) 安全标准委员会](https://www.pcisecuritystandards.org/pci_security/)定义，作为 [PCI 数据安全标准 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)的一部分。 请参阅 PCI DSS，了解每项要求的测试过程和指南。

若要确保关键数据只能由经过授权的人员访问，必须设置系统和进程，以基于“需要知道”和根据工作职责限制访问。

当仅授予对执行作业所需的最少量数据和特权的访问权限时“需要知道”。

## <a name="pci-dss-requirement-71"></a>PCI DSS 要求 7.1

**7.1** 将对系统组件和持卡人数据的访问限制为只有那些其工作需要进行此类访问的个人可以访问。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Azure 针对以下项强制实施现有 ISMS 策略：Azure 人员对 Azure 系统组件的访问，验证访问控制的有效性，提供实时管理访问权限，不再需要时撤消访问权限，以及确保访问 Azure 平台环境的工作人员具有业务需要。 严格限制 Azure 访问客户环境，仅在客户批准的情况下允许。<br /><br />已建立相关规程，将对数据中心的物理访问仅限于授权的员工、供应商、承包商和访问者。 需要对内部数据中心设施进行临时访问的人员需要进行安全验证和签入。 Azure 团队每个季度都会检查物理访问日志。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责将对系统组件和持卡人数据的访问限制为只有那些其工作需要进行此类访问的个人可以访问。 这包括限制对 Azure 管理门户的访问以及指定有权创建、修改或删除 PaaS 服务的帐户或角色。|



### <a name="pci-dss-requirement-711"></a>PCI DSS 要求 7.1.1

**7.1.1** 定义每个角色所需的访问权限，包括：
- 每个角色由于其工作职能需要访问的系统组件和数据资源
- 访问资源所需的权限级别（例如，用户、管理员等）

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责定义和记录用户 ID 审批过程、定义最少特权、限制对持卡人数据的访问、使用唯一 ID、提供职责分离，以及在不再需要时撤消用户访问权限。|



### <a name="pci-dss-requirement-712"></a>PCI DSS 要求 7.1.2

**7.1.2** 将特权用户 ID 的访问权限限制为执行工作职责所需的最少特权。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 已采用适用的公司和组织安全策略，包括信息安全策略。 这些策略已经批准、发布并传送到 Windows Azure。 Microsoft Azure 信息安全策略要求基于业务理由及“需要知道”和“最小特权”原则，通过资产所有者授权授予对 Microsoft Azure 资产的访问权限。 该策略还满足访问管理生命周期的要求，包括访问权限预配、访问授权、访问权限的身份验证去除和定期访问审核。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 在部署期间创建三个帐户：admin、sqladmin 和 edna（执行演示期间登录到 Web 应用的默认用户）。 用户角色仅限于基于记录的演示方案的职责。|



### <a name="pci-dss-requirement-713"></a>PCI DSS 要求 7.1.3

**7.1.3** 根据单个人员的工作分类和职能分配访问权限。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 在部署期间创建三个帐户：admin、sqladmin 和 edna（执行演示期间登录到 Web 应用的默认用户）。 用户角色仅限于基于记录的演示方案的职责。|



### <a name="pci-dss-requirement-714"></a>PCI DSS 要求 7.1.4

**7.1.4** 需要授权方有案可稽的批准并指定所需的特权。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责将对系统组件和持卡人数据的访问限制为只有那些其工作需要进行此类访问的个人可以访问。 这包括限制对 Azure 管理门户的访问以及指定有权创建、修改或删除 PaaS 服务的帐户或角色。|



## <a name="pci-dss-requirement-72"></a>PCI DSS 要求 7.2

**7.2** 为系统组件建立访问控制系统，该系统基于用户的需要知道信息限制访问，并且除非专门允许否则设置为“全部拒绝”。
此访问控制系统必须包括以下项：
- 7.2.1 覆盖所有系统组件。
- 7.2.2 根据工作分类和职能为个人分配权限。
- 7.2.3 默认“全部拒绝”设置。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 Azure Active Directory 将访问限制为只能指定用户才能访问。 有关详细信息，请参阅 [PCI 指南 - 标识管理](payment-processing-blueprint.md#identity-management)。|



## <a name="pci-dss-requirement-73"></a>PCI DSS 要求 7.3

**7.3** 确保用于限制对持卡人数据的访问的安全策略和操作过程都已记录在案、在使用中并对所有受影响方已知。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br />(Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 文档就谁使用 CHD 以及如何使用 CHD 提供用例和说明。|




