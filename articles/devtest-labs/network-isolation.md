---
title: Azure 开发测试实验室中的网络隔离
description: 了解 Azure 开发测试实验室中的网络隔离。
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: fbd2725cd3677e03cadbe0ae1f060b141f5d212b
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875657"
---
# <a name="network-isolation-in-devtest-labs"></a>开发测试实验室中的网络隔离

[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)充当安全边界，将 azure 资源与公共 internet 隔离开来。 你还可以将 Azure 虚拟网络加入本地网络，以便能够安全地连接到本地资源。 在开发测试实验室中，可以选择将 [所有实验室虚拟机](devtest-lab-configure-vnet.md) 和 [环境隔离到网络](connect-environment-lab-virtual-network.md) ，以确保实验室资源遵循组织网络策略。 

作为实验室所有者，你还可以选择完全隔离实验室，也就是说，将虚拟机和环境隔离到所选网络后，还可以隔离在你的订阅中创建的实验室存储帐户和密钥保管库。 本文逐步讲解如何创建网络隔离实验室。 

另外，请查看以下文章：

- [开发测试实验室如何使用实验室存储帐户](encrypt-storage.md)
- [开发测试实验室如何使用密钥保管库](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> 目前仅支持新的实验室创建的网络隔离。

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>在创建实验室期间启用网络隔离的步骤

1. 在创建实验室的过程中，请切换到 " **网络** " 选项卡。
1. 您可以选择实验室将为您创建的 **默认** 网络，也可以从下拉端选择现有网络。 你将只能选择与实验室处于同一区域和订阅中的网络。 

    > [!div class="mx-imgBorder"]
    > ![创建实验室](./media/network-isolation/create-lab.png)
1. 选择子网。

    > [!div class="mx-imgBorder"]
    > ![创建子网](./media/network-isolation/create-lab-subnet.png)
1. 如果选择将实验室资源 (实验室存储帐户和 key vault) 隔离到默认网络，则无需执行进一步的操作，并且实验室将处理今后的隔离资源。
 
    > [!div class="mx-imgBorder"]
    > ![网络隔离](./media/network-isolation/isolate-lab-resources.png)
1. 如果选择将实验室资源 (实验室存储帐户和 key vault) 隔离到你选择的现有网络，则需要完成实验室创建后的以下步骤，以确保实验室在隔离模式下继续运行。 
 
    > [!div class="mx-imgBorder"]
    > ![隔离资源](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > 实验室所有者需要在配置和/或在实验室中创建任何资源之前完成这些步骤。

### <a name="steps-to-follow-post-lab-creation"></a>完成实验室创建后的步骤

1. 在实验室的 "主页" 页上，选择 "**概述**" 页上的**资源组**。 应会看到包含实验室的资源组的 " **资源组** " 页。 
 
   > [!div class="mx-imgBorder"]
   > ![Contoso 实验室](./media/network-isolation/contoso-lab.png)
1. 选择实验室的 Azure 存储帐户。 实验室存储帐户的命名约定为：<*labNameWithoutInvalidCharacters* > *<4 位数*>。 例如，如果实验室名称为 contosolab，则存储帐户名称可以是 acontosolab1234。
 
   > [!div class="mx-imgBorder"]
   > ![Contoso 测试](./media/network-isolation/contoso-test.png)
1. 在存储帐户上，请参阅 "防火墙和虚拟网络"，并确保选中 "允许受信任的 Microsoft 服务访问此存储帐户" 复选框。 由于 [开发测试实验室是受信任的 Microsoft 服务](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)，此选项可让实验室在网络隔离模式下正常运行。 

   > [!div class="mx-imgBorder"]
   > ![Contoso 实验室防火墙](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. 接下来，单击 " **+ 添加现有虚拟网络**"，选择创建实验室时选取的虚拟网络和子网，然后单击 " **启用**"。 

   > [!div class="mx-imgBorder"]
   > ![Contoso 我的 vnet](./media/network-isolation/contoso-lab-my-vnet.png)
5.  成功为选定的虚拟网络启用服务终结点后，请单击 " **添加**"。 

   > [!div class="mx-imgBorder"]
   > ![添加](./media/network-isolation/contoso-firewall-add.png)
 
这样，Azure 存储将允许来自添加的虚拟网络的入站连接，并使实验室在网络隔离模式下成功运行。 

你还可以选择自动执行这些步骤，为多个实验室配置此设置。 

[详细了解如何使用 PowerShell 和 CLI 管理 Azure 存储的默认网络访问规则](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=/azure/virtual-network/toc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>在网络隔离模式下使用实验室时要记住的问题

### <a name="accessing-labs-storage-account-outside-the-lab"></a>在实验室外访问实验室的存储帐户 

在网络隔离实验室中，对于将 VHD 上传到实验室的存储帐户以从其创建自定义映像的操作，实验室所有者需要显式启用从允许的终结点访问存储帐户。 为此，可以创建一个虚拟机，并从该虚拟机安全地访问实验室的存储帐户。 

[详细了解如何从虚拟机访问存储帐户](../private-link/create-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>从实验室导出使用情况数据 

在网络隔离实验室中，若要 [导出实验室的个人使用情况数据](personal-data-delete-export.md)，实验室所有者将显式需要提供存储帐户，并在该帐户中生成一个 blob 来存储数据。 

如果未提供存储帐户，此操作将在网络隔离模式下失败，因为实验室的存储帐户无法供实验室使用，以防客户提供存储帐户。 

[了解有关在指定的存储帐户中导出实验室使用情况数据的详细信息](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>后续步骤

[使用 Azure 资源管理器模板和 PowerShell 自动创建或修改实验室](devtest-lab-use-arm-and-powershell-for-lab-resources.md)
