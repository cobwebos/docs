---
title: 使用云解决方案提供商 (CSP) 计划管理 Azure Site Recovery 中的多租户 | Microsoft Docs
description: 介绍如何通过 CSP 创建和管理 租户订阅，并在多租户设置中部署 Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 6dc8b573e66eaae1b5cb923ae72333fb959d0969
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767632"
---
# <a name="manage-multi-tenancy-with-the-cloud-solution-provider-csp-program"></a>使用云解决方案提供商 (CSP) 计划管理多租户

[CSP 计划](https://partner.microsoft.com/en-US/cloud-solution-provider)可促进 Microsoft 云服务（包括 Office 365、企业移动性套件和 Microsoft Azure）更好地结合在一起。 有了 CSP，合作伙伴就可以与客户建立端到端关系，并在建立关系的过程中充当主要联络点。 合作伙伴可以为客户部署 Azure 订阅，并将这些订阅与自己的增值型自定义产品/服务相结合。

有了 Azure Site Recovery，合作伙伴就可以直接通过 CSP 为客户管理完整的灾难恢复解决方案。 合作伙伴也可以通过 CSP 来设置 Site Recovery 环境，让客户以自助方式管理自己的灾难恢复需求。 在这两种方案中，合作伙伴充当联系 Site Recovery 和客户的纽带。 合作伙伴提供客户关系服务，并针对 Site Recovery 使用情况向客户收费。

本文介绍如何通过 CSP 创建和管理租户订阅，以便进行多租户 VMware 设置。

## <a name="prerequisites"></a>先决条件

- [准备](tutorial-prepare-azure.md) Azure 资源，其中包括 Azure 订阅、Azure 虚拟网络和存储帐户。
- [准备](tutorial-prepare-on-premises-vmware.md) VMware 本地 VMware 服务器和 VM。
- 为每个租户创建单独的管理服务器，以便与租户 VM 以及合作伙伴的 vCenter 通信。 仅合作伙伴具有该服务器的访问权限。 有关不同多租户环境的更多详细信息，请参阅[多租户 VMware](site-recovery-multi-tenant-support-vmware-using-csp.md) 指南。

## <a name="create-a-tenant-account"></a>创建租户帐户

1. 通过 [Microsoft 合作伙伴中心](https://partnercenter.microsoft.com/)登录到 CSP 帐户。

2. 在“仪表板”菜单上，选择“客户”。

    ![Microsoft 合作伙伴中心客户链接](./media/site-recovery-manage-multi-tenancy-with-csp/csp-dashboard-display.png)

3. 在打开的页面上，单击“添加客户”按钮。

    ![“添加客户”按钮](./media/site-recovery-manage-multi-tenancy-with-csp/add-new-customer.png)

4. 在“新建客户”页上，填写租户的帐户信息细节，然后单击“下一步: 订阅”。

    ![“帐户信息”页](./media/site-recovery-manage-multi-tenancy-with-csp/customer-add-filled.png)

5. 在订阅选择页上，选中“Microsoft Azure”复选框。 可以现在就添加其他订阅，也可以选择在其他时间添加。

    ![Microsoft Azure 订阅复选框](./media/site-recovery-manage-multi-tenancy-with-csp/azure-subscription-selection.png)

6. 在“审阅”页上，确认租户详细信息，然后单击“提交”。

    ![“审阅”页](./media/site-recovery-manage-multi-tenancy-with-csp/customer-summary-page.png)  

    创建租户帐户以后，会显示确认页，其中显示默认帐户的详细信息，以及该订阅的密码。

7. 保存信息，以后可根据需要通过 Azure 门户登录页更改密码。  

    可以将该信息与租户原样共享，也可以根据需要创建单独的帐户并进行共享。

## <a name="access-the-tenant-account"></a>访问租户帐户

可以通过 Microsoft 合作伙伴中心仪表板访问租户的订阅，如“步骤 1：创建租户帐户”中所述。

1. 转到“客户”页，然后单击租户帐户的名称。

2. 在租户帐户的“订阅”页上，可以监视现有的帐户订阅，并根据需要添加更多订阅。 若要管理租户的灾难恢复操作，请选择“所有资源(Azure 门户)”。

    ![“所有资源”链接](./media/site-recovery-manage-multi-tenancy-with-csp/all-resources-select.png)  

    单击“所有资源”即可获得访问租户的 Azure 订阅的授权。 单击 Azure 门户右上角的 Azure Active Directory 链接即可验证访问权限。

    ![Azure Active Directory 链接](./media/site-recovery-manage-multi-tenancy-with-csp/aad-admin-display.png)

用户现在可以通过 Azure 门户执行针对租户的所有站点恢复操作，以及管理灾难恢复操作。 若要通过 CSP 来访问租户订阅以实现托管式灾难恢复，请按前述过程操作。

## <a name="deploy-resources-to-the-tenant-subscription"></a>将资源部署到租户订阅
1. 在 Azure 门户中，按照常规过程创建资源组，然后部署恢复服务保管库。

2. 下载保管库注册密钥。

3. 使用保管库注册密钥为租户注册 CS。

4. 输入两个访问帐户（vCenter 访问帐户和 VM 访问帐户）的凭据。

    ![管理配置服务器帐户](./media/site-recovery-manage-multi-tenancy-with-csp/config-server-account-display.png)

## <a name="register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>将 Site Recovery 基础结构注册到恢复服务保管库
1. 在 Azure 门户的此前创建的保管库中，将 vCenter 服务器注册到已在“步骤 3：将资源部署到租户订阅”中注册的 CS。 将 vCenter 访问帐户用于此目的。
2. 按照常规过程完成 Site Recovery 的“准备基础结构”过程。
3. VM 现在可以进行复制了。 验证在“复制”选项下的“选择虚拟机”边栏选项卡上，是否只有租户的 VM 显示。

    ![“选择虚拟机”边栏选项卡上的“租户 VM”列表](./media/site-recovery-manage-multi-tenancy-with-csp/tenant-vm-display.png)

## <a name="assign-tenant-access-to-the-subscription"></a>分配租户对订阅的访问权限

对于自助式灾难恢复，请为租户提供帐户详细信息，如“步骤 1：创建租户帐户”部分中的步骤 6 所述。 请在合作伙伴设置灾难恢复基础结构后执行此操作。 不管灾难恢复类型是托管式还是自助式，合作伙伴都必须通过 CSP 门户访问租户订阅。 合作伙伴设置其所拥有的保管库，并将基础结构注册到租户订阅。

合作伙伴也可通过 CSP 门户将新用户添加到租户订阅，操作步骤如下：

1. 转到租户的 CSP 订阅页，然后选择“用户和许可证”选项。

    ![租户的 CSP 订阅页](./media/site-recovery-manage-multi-tenancy-with-csp/users-and-licences.png)

    现在可以创建新的用户，只需输入相关详细信息并选择权限，或者通过 CSV 文件上传用户列表即可。

2. 创建新用户之后，请返回到 Azure 门户，然后在“订阅”边栏选项卡上选择相关订阅。

3. 在打开的边栏选项卡上选择“访问控制(IAM)”，然后单击“+添加”添加具有相关访问级别的用户。      
    通过 CSP 门户创建的用户会自动显示在单击一种访问级别后打开的边栏选项卡上。

    ![添加用户](./media/site-recovery-manage-multi-tenancy-with-csp/add-user-subscription.png)

    对于大多数管理操作，“参与者”角色已足够。 具有此访问级别的用户可以对订阅执行所有操作，更改访问级别除外（此操作需要“所有者”级别访问权限）。

  Azure Site Recovery 还具有三个[预定义的用户角色](site-recovery-role-based-linked-access-control.md)，这些角色可用于根据需要进一步限制访问级别。

## <a name="next-steps"></a>后续步骤
  [详细了解](site-recovery-role-based-linked-access-control.md)使用基于角色的访问控制管理 Azure Site Recovery 部署。

  [管理多租户 VMware 环境](site-recovery-multi-tenant-support-vmware-using-csp.md)
