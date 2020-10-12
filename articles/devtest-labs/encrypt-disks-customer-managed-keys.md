---
title: 使用 Azure 开发测试实验室中客户托管的密钥加密 OS 磁盘
description: 了解如何使用 Azure 开发测试实验室中的客户托管密钥来加密操作系统 (OS) 磁盘。
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 257894c6318c9ca083c72daf3c888f7d509ae683
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89489798"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>使用 Azure 开发测试实验室中的客户托管密钥来加密操作系统 (OS) 磁盘
服务器端加密 (SSE) 可保护数据，并帮助实现组织安全性和符合性承诺。 默认情况下，SSE 自动加密存储在 (Azure 中托管磁盘上的数据，默认情况下，在将其保存到云时) 静态。 了解有关 Azure 上的 [磁盘加密](../virtual-machines/windows/disk-encryption.md) 的详细信息。 

在开发测试实验室中，所有作为实验室的一部分创建的 OS 磁盘和数据磁盘都使用平台托管的密钥进行加密。 但作为实验室所有者，你可以选择使用自己的密钥加密实验室虚拟机 OS 磁盘。 如果选择使用自己的密钥管理加密，则可以指定 **客户托管的密钥** ，以用于在实验室 OS 磁盘中对数据进行加密。 若要详细了解 (SSE) 与客户托管的密钥以及其他托管磁盘加密类型的服务器端加密，请参阅 [客户管理的密钥](../virtual-machines/windows/disk-encryption.md#customer-managed-keys)。 另请参阅 [使用客户管理的密钥的限制](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions)。

> [!NOTE]
> - 目前仅支持使用客户托管密钥的磁盘加密，开发测试实验室中的 OS 磁盘。 
> 
> - 此设置适用于实验室中新创建的 OS 磁盘。 如果选择在某个时间点更改磁盘加密集，实验室中较早的磁盘将继续使用以前的磁盘加密集进行加密。 

以下部分说明实验室所有者如何使用客户管理的密钥设置加密。

## <a name="pre-requisites"></a>先决条件

1. 如果未设置磁盘加密，请遵循本文 [设置 Key Vault 和磁盘加密集](../virtual-machines/disks-enable-customer-managed-keys-portal.md)。 请注意磁盘加密集的以下要求： 

    - 磁盘加密集需要与 **实验室位于同一区域和订阅中**。 
    - 确保 (实验室所有者) 至少具有对将用于加密实验室 OS 磁盘的磁盘加密集的 **读取器级别的访问权限** 。 
1. 对于在8/1/2020 之前创建的实验室，实验室所有者需要确保已启用实验室系统分配的标识。 为此，实验室所有者可以转到其实验室，单击 " **配置和策略**"，单击 " **标识" (预览 ") ** 边栏选项卡，将" 系统分配的标识 **状态** "更改为 **" 打开** "，然后单击" **保存**"。 对于在8/1/2020 实验室的系统分配标识之后创建的新实验室，默认情况下将启用。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="托管密钥&quot;:::
1. 为了使实验室能够处理所有实验室 OS 磁盘的加密，实验室所有者需要在底层 Azure 订阅上显式授予实验室的 **系统分配的标识** 读取者角色，以及虚拟机参与者角色。 实验室所有者可以通过完成以下步骤来执行此操作：

   
    1. 确保你是 Azure 订阅级别的 &quot; [用户访问管理员&quot; 角色](../role-based-access-control/built-in-roles.md#user-access-administrator) 的成员，以便你可以管理用户对 azure 资源的访问权限。 
    1. 在 &quot; **磁盘加密集** &quot; 页上，选择左侧菜单中的 &quot; **访问控制 (IAM) ** 。 
    1. 在工具栏上选择 &quot; **+ 添加** &quot;，然后选择 " **添加角色分配**"。  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="托管密钥&quot;:::
1. 为了使实验室能够处理所有实验室 OS 磁盘的加密，实验室所有者需要在底层 Azure 订阅上显式授予实验室的 **系统分配的标识** 读取者角色，以及虚拟机参与者角色。 实验室所有者可以通过完成以下步骤来执行此操作：

   
    1. 确保你是 Azure 订阅级别的 &quot; [用户访问管理员&quot; 角色](../role-based-access-control/built-in-roles.md#user-access-administrator) 的成员，以便你可以管理用户对 azure 资源的访问权限。 
    1. 在 &quot; **磁盘加密集** &quot; 页上，选择左侧菜单中的 &quot; **访问控制 (IAM) ** 。 
    1. 在工具栏上选择 &quot; **+ 添加** &quot;，然后选择 " 角色或角色。 
    1. 键入要为其使用磁盘加密集的实验室名称，并从下拉列表中为实验室) 选择实验室名称 (系统分配的标识。 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="托管密钥&quot;:::
1. 为了使实验室能够处理所有实验室 OS 磁盘的加密，实验室所有者需要在底层 Azure 订阅上显式授予实验室的 **系统分配的标识** 读取者角色，以及虚拟机参与者角色。 实验室所有者可以通过完成以下步骤来执行此操作：

   
    1. 确保你是 Azure 订阅级别的 &quot; [用户访问管理员&quot; 角色](../role-based-access-control/built-in-roles.md#user-access-administrator) 的成员，以便你可以管理用户对 azure 资源的访问权限。 
    1. 在 &quot; **磁盘加密集** &quot; 页上，选择左侧菜单中的 &quot; **访问控制 (IAM) ** 。 
    1. 在工具栏上选择 &quot; **+ 添加** &quot;，然后选择 ":::        
    1. 在工具栏上选择“保存”。 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="托管密钥&quot;:::
1. 为了使实验室能够处理所有实验室 OS 磁盘的加密，实验室所有者需要在底层 Azure 订阅上显式授予实验室的 **系统分配的标识** 读取者角色，以及虚拟机参与者角色。 实验室所有者可以通过完成以下步骤来执行此操作：

   
    1. 确保你是 Azure 订阅级别的 &quot; [用户访问管理员&quot; 角色](../role-based-access-control/built-in-roles.md#user-access-administrator) 的成员，以便你可以管理用户对 azure 资源的访问权限。 
    1. 在 &quot; **磁盘加密集** &quot; 页上，选择左侧菜单中的 &quot; **访问控制 (IAM) ** 。 
    1. 在工具栏上选择 &quot; **+ 添加** &quot;，然后选择 " **添加角色分配**"。 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="托管密钥&quot;:::
1. 为了使实验室能够处理所有实验室 OS 磁盘的加密，实验室所有者需要在底层 Azure 订阅上显式授予实验室的 **系统分配的标识** 读取者角色，以及虚拟机参与者角色。 实验室所有者可以通过完成以下步骤来执行此操作：

   
    1. 确保你是 Azure 订阅级别的 &quot; [用户访问管理员&quot; 角色](../role-based-access-control/built-in-roles.md#user-access-administrator) 的成员，以便你可以管理用户对 azure 资源的访问权限。 
    1. 在 &quot; **磁盘加密集** &quot; 页上，选择左侧菜单中的 &quot; **访问控制 (IAM) ** 。 
    1. 在工具栏上选择 &quot; **+ 添加** &quot;，然后选择 " **虚拟机参与者** "。
    1. 键入 "实验室名称"，然后从下拉列表中选择实验室 **名称** (系统为实验室) 指定的标识。 
    1. 在工具栏上选择“保存”。 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>使用客户管理的密钥加密实验室 OS 磁盘 

1. 在 Azure 门户的实验室主页上，选择左侧菜单中的 " **配置和策略** "。 
1. 在 "**配置和策略**" 页上的 "**加密**" 部分中，选择 "**磁盘 (预览") ** 。 默认情况下，**使用平台托管密钥**将**加密类型**设置为 "静态加密"。

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="托管密钥&quot;:::
1. 为了使实验室能够处理所有实验室 OS 磁盘的加密，实验室所有者需要在底层 Azure 订阅上显式授予实验室的 **系统分配的标识** 读取者角色，以及虚拟机参与者角色。 实验室所有者可以通过完成以下步骤来执行此操作：

   
    1. 确保你是 Azure 订阅级别的 &quot; [用户访问管理员&quot; 角色](../role-based-access-control/built-in-roles.md#user-access-administrator) 的成员，以便你可以管理用户对 azure 资源的访问权限。 
    1. 在 &quot; **磁盘加密集** &quot; 页上，选择左侧菜单中的 &quot; **访问控制 (IAM) ** 。 
    1. 在工具栏上选择 &quot; **+ 添加** &quot;，然后选择 " **磁盘加密集**"，请选择之前创建的磁盘加密集。 它与系统分配的实验室标识可访问的磁盘加密集相同。
1. 在工具栏上选择“保存”。 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="托管密钥&quot;:::
1. 为了使实验室能够处理所有实验室 OS 磁盘的加密，实验室所有者需要在底层 Azure 订阅上显式授予实验室的 **系统分配的标识** 读取者角色，以及虚拟机参与者角色。 实验室所有者可以通过完成以下步骤来执行此操作：

   
    1. 确保你是 Azure 订阅级别的 &quot; [用户访问管理员&quot; 角色](../role-based-access-control/built-in-roles.md#user-access-administrator) 的成员，以便你可以管理用户对 azure 资源的访问权限。 
    1. 在 &quot; **磁盘加密集** &quot; 页上，选择左侧菜单中的 &quot; **访问控制 (IAM) ** 。 
    1. 在工具栏上选择 &quot; **+ 添加** &quot;，然后选择 ":::
1. 在包含以下文本的消息框上： *此设置将应用于实验室中新创建的计算机。旧操作系统磁盘将在旧的磁盘加密集中保持加密*，请选择 **"确定"**。 

    配置完成后，将使用磁盘加密集提供的客户托管密钥来加密实验室操作系统磁盘。 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>如何验证是否正在加密磁盘

1. 当使用实验室上的客户托管密钥启用磁盘加密后，请使用创建的实验室虚拟机。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="托管密钥&quot;:::
1. 为了使实验室能够处理所有实验室 OS 磁盘的加密，实验室所有者需要在底层 Azure 订阅上显式授予实验室的 **系统分配的标识** 读取者角色，以及虚拟机参与者角色。 实验室所有者可以通过完成以下步骤来执行此操作：

   
    1. 确保你是 Azure 订阅级别的 &quot; [用户访问管理员&quot; 角色](../role-based-access-control/built-in-roles.md#user-access-administrator) 的成员，以便你可以管理用户对 azure 资源的访问权限。 
    1. 在 &quot; **磁盘加密集** &quot; 页上，选择左侧菜单中的 &quot; **访问控制 (IAM) ** 。 
    1. 在工具栏上选择 &quot; **+ 添加** &quot;，然后选择 ":::
1. 单击 VM 的资源组，并单击 OS 磁盘。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="托管密钥&quot;:::
1. 为了使实验室能够处理所有实验室 OS 磁盘的加密，实验室所有者需要在底层 Azure 订阅上显式授予实验室的 **系统分配的标识** 读取者角色，以及虚拟机参与者角色。 实验室所有者可以通过完成以下步骤来执行此操作：

   
    1. 确保你是 Azure 订阅级别的 &quot; [用户访问管理员&quot; 角色](../role-based-access-control/built-in-roles.md#user-access-administrator) 的成员，以便你可以管理用户对 azure 资源的访问权限。 
    1. 在 &quot; **磁盘加密集** &quot; 页上，选择左侧菜单中的 &quot; **访问控制 (IAM) ** 。 
    1. 在工具栏上选择 &quot; **+ 添加** &quot;，然后选择 " 设置为所选的磁盘加密集。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="托管密钥&quot;:::
1. 为了使实验室能够处理所有实验室 OS 磁盘的加密，实验室所有者需要在底层 Azure 订阅上显式授予实验室的 **系统分配的标识** 读取者角色，以及虚拟机参与者角色。 实验室所有者可以通过完成以下步骤来执行此操作：

   
    1. 确保你是 Azure 订阅级别的 &quot; [用户访问管理员&quot; 角色](../role-based-access-control/built-in-roles.md#user-access-administrator) 的成员，以便你可以管理用户对 azure 资源的访问权限。 
    1. 在 &quot; **磁盘加密集** &quot; 页上，选择左侧菜单中的 &quot; **访问控制 (IAM) ** 。 
    1. 在工具栏上选择 &quot; **+ 添加** &quot;，然后选择 ":::
  
## <a name="next-steps"></a>后续步骤

请参阅以下文章： 

- [Azure 磁盘加密](../virtual-machines/windows/disk-encryption.md)。 
- [客户管理的密钥](../virtual-machines/windows/disk-encryption.md#customer-managed-keys) 
