---
title: 使用 Site Recovery 和云解决方案提供商 (CSP) 计划在多租户环境中设置到 Azure 的 VMware 复制 | Microsoft Docs
description: 介绍如何通过 CSP 创建和管理 租户订阅，并在多租户设置中部署 Azure Site Recovery
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: d384a77054cdcab0305b9e6d3fe5bb824e49bb16
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916796"
---
# <a name="set-up-vmware-replication-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>使用云解决方案提供商 (CSP) 计划在多租户环境中设置 VMware 复制

[CSP 计划](https://partner.microsoft.com/en-US/cloud-solution-provider)可促进 Microsoft 云服务（包括 Office 365、企业移动性套件和 Microsoft Azure）更好地结合在一起。 有了 CSP，合作伙伴就可以与客户建立端到端关系，并在建立关系的过程中充当主要联络点。 合作伙伴可以为客户部署 Azure 订阅，并将这些订阅与自己的增值型自定义产品/服务相结合。

有了 [Azure Site Recovery](site-recovery-overview.md)，作为合作伙伴，你可以直接通过 CSP 为客户管理灾难恢复。 另外，你也可以通过 CSP 来设置 Site Recovery 环境，让客户以自助方式管理自己的灾难恢复需求。 在这两种方案中，合作伙伴充当联系 Site Recovery 和客户的纽带。 合作伙伴提供客户关系服务，并针对 Site Recovery 使用情况向客户收费。

本文介绍了你作为合作伙伴如何通过 CSP 为多租户 VMware 方案创建和管理租户订阅。

## <a name="prerequisites"></a>先决条件

若要设置 VMware 复制，需要执行以下操作：

- [准备](tutorial-prepare-azure.md) Azure 资源，其中包括 Azure 订阅、Azure 虚拟网络和存储帐户。
- [准备](vmware-azure-tutorial-prepare-on-premises.md)本地 VMware 服务器和 VM。
- 为每个租户创建单独的管理服务器，以便与租户 VM 以及你的 vCenter 服务器进行通信。 只有作为合作伙伴的你应当具有对此管理服务器的访问权限。 详细了解[多租户环境](vmware-azure-multi-tenant-overview.md)。

## <a name="create-a-tenant-account"></a>创建租户帐户

1. 通过 [Microsoft 合作伙伴中心](https://partnercenter.microsoft.com/)登录到 CSP 帐户。
2. 在“仪表板”菜单上，选择“客户”。
3. 在打开的页面上，单击“添加客户”按钮。
4. 在“新建客户”页上，填写租户的帐户信息细节。

    ![“帐户信息”页](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. 然后单击“下一步: 订阅”。
6. 在订阅选择页上，选中“Microsoft Azure”复选框。 可以现在就添加其他订阅，也可以选择在其他时间添加。
7. 在“审阅”页上，确认租户详细信息，然后单击“提交”。
8. 创建租户帐户以后，会显示确认页，其中显示默认帐户的详细信息，以及该订阅的密码。 保存信息，以后可根据需要通过 Azure 门户登录页更改密码。

可以将该信息与租户原样共享，也可以根据需要创建单独的帐户并进行共享。

## <a name="access-the-tenant-account"></a>访问租户帐户

可以通过 Microsoft 合作伙伴中心仪表板访问租户的订阅。

1. 在“客户”页上，单击租户帐户的名称。
2. 在租户帐户的“订阅”页上，可以监视现有的帐户订阅，并根据需要添加更多订阅。
3. 若要管理租户的灾难恢复操作，请选择“所有资源(Azure 门户)”。 这将向你授予对租户的 Azure 订阅的访问权限。

    ![“所有资源”链接](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. 可以单击 Azure 门户右上角的 Azure Active Directory 链接来验证访问权限。

    ![Azure Active Directory 链接](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

现在可以在 Azure 门户中为租户执行和管理所有 Site Recovery 操作。 若要通过 CSP 来访问租户订阅以实现托管式灾难恢复，请按前述过程操作。

## <a name="assign-tenant-access-to-the-subscription"></a>分配租户对订阅的访问权限

1. 确保设置灾难恢复基础结构。 不管灾难恢复是托管式还是自助式，合作伙伴都通过 CSP 门户访问租户订阅。 设置保管库，并将基础结构注册到租户订阅。
2. 向租户提供[你创建的帐户](#create-a-tenant-account)。
3. 你可以通过 CSP 门户将新用户添加到租户订阅，如下所示：

    a) 转到租户的 CSP 订阅页，然后选择“用户和许可证”选项。

      ![租户的 CSP 订阅页](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

      b) 现在可以创建新的用户，只需输入相关详细信息并选择权限，或者通过 CSV 文件上传用户列表即可。
    c) 创建新用户后，返回到 Azure 门户。 在“订阅”页上，选择相关的订阅。
    d) 选择“访问控制(IAM)”，然后单击“添加”以添加具有相关访问级别的用户。 通过 CSP 门户创建的用户会自动显示在单击一种访问级别后打开的页面上。

      ![添加用户](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- 对于大多数管理操作，“参与者”角色已足够。 具有此访问级别的用户可以对订阅执行所有操作，更改访问级别除外（此操作需要“所有者”级别访问权限）。
- Site Recovery 还具有三个[预定义的用户角色](site-recovery-role-based-linked-access-control.md)，这些角色可用于根据需要进一步限制访问级别。

## <a name="multi-tenant-environments"></a>多租户环境

有三种主要的多租户模型：

* **共享托管服务提供程序 (HSP)**：合作伙伴拥有物理基础结构，并使用共享资源（vCenter、数据中心、物理存储等）在同一基础结构上托管多个租户的 VM。 合作伙伴可以提供灾难恢复管理作为托管服务，租户也可以拥有灾难恢复作为自助解决方案。

* **专用托管服务提供程序**：合作伙伴拥有物理基础结构，但使用专用资源（多个 vCenter、物理数据存储等）在单独的基础结构上托管每个租户的 VM。 合作伙伴可以提供灾难恢复管理作为托管服务，租户也可以拥有它作为自助解决方案。

* **托管服务提供程序 (MSP)**：客户拥有托管 VM 的物理基础结构，合作伙伴提供灾难恢复启用和管理功能。

通过按本文所述设置租户订阅，可以快速在任何相关的多租户模型中开始启用客户。 可以在[此处](vmware-azure-multi-tenant-overview.md)了解有关不同多租户模型和启用本地访问控制的详细信息。

## <a name="next-steps"></a>后续步骤
- 详细了解[使用基于角色的访问控制](site-recovery-role-based-linked-access-control.md)管理 Azure Site Recovery 部署。
- 详细了解 VMware 到 Azure [复制体系结构](vmware-azure-architecture.md)。
- 查看有关将 VMware VM 复制到 Azure 的[教程](vmware-azure-tutorial.md)。
详细了解用于将 VMware VM 复制到 Azure 的[多租户环境](vmware-azure-multi-tenant-overview.md)。
