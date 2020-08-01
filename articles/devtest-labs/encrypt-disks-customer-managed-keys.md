---
title: 使用 Azure 开发测试实验室中客户托管的密钥加密 OS 磁盘
description: 了解如何使用 Azure 开发测试实验室中客户托管的密钥对操作系统（OS）磁盘进行加密。
ms.topic: article
ms.date: 07/28/2020
ms.openlocfilehash: 153d27061814969964c9340cd85cad92bfdbc7d2
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87462308"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>使用 Azure 开发测试实验室中的客户托管密钥对操作系统（OS）磁盘进行加密
服务器端加密 (SSE) 可保护数据，并帮助实现组织安全性和符合性承诺。 默认情况下，在将数据保存到云时，SSE 会自动加密存储在 Azure 中的托管磁盘上的数据（OS 和数据磁盘）。 了解有关 Azure 上的[磁盘加密](../virtual-machines/windows/disk-encryption.md)的详细信息。 

在开发测试实验室中，所有作为实验室的一部分创建的 OS 磁盘和数据磁盘都使用平台托管的密钥进行加密。 但作为实验室所有者，你可以选择使用自己的密钥加密实验室虚拟机 OS 磁盘。 如果选择使用自己的密钥管理加密，则可以指定**客户托管的密钥**，以用于在实验室 OS 磁盘中对数据进行加密。 若要了解有关客户托管密钥的服务器端加密（SSE）和其他托管磁盘加密类型的详细信息，请参阅[客户管理的密钥](../virtual-machines/windows/disk-encryption.md#customer-managed-keys)。 另请参阅[使用客户管理的密钥的限制](/virtual-machines/windows/disks-enable-customer-managed-keys-portal.md#restrictions)。


> [!NOTE]
> - 目前仅支持使用客户托管密钥的磁盘加密，开发测试实验室中的 OS 磁盘。 
> 
> - 此设置适用于实验室中新创建的 OS 磁盘。 如果选择在某个时间点更改磁盘加密集，实验室中较早的磁盘将继续使用以前的磁盘加密集进行加密。 

以下部分说明实验室所有者如何使用客户管理的密钥设置加密。

## <a name="pre-requisites"></a>先决条件

- 磁盘加密集需要与实验室位于同一区域和订阅中。 
- 确保（实验室所有者）至少具有对将用于加密实验室 OS 磁盘的磁盘加密集的**读取器级别的访问权限**。 如果未设置磁盘加密，请遵循本文[设置 Key Vault 和磁盘加密集](../virtual-machines/windows/disks-enable-customer-managed-keys-portal.md#set-up-your-azure-key-vault)。 
- 为了使实验室能够处理所有实验室 OS 磁盘的加密，实验室所有者需要向实验室的系统分配的标识显式授予对磁盘加密集的权限。 实验室所有者可以通过完成以下步骤来执行此操作：
    1. 确保你是 Azure 订阅级别的 "[用户访问管理员" 角色](/role-based-access-control/built-in-roles.md#user-access-administrator)的成员，以便你可以管理用户对 azure 资源的访问权限。 
    1. 在 "**磁盘加密集**" 页上，选择左侧菜单中的 "**访问控制（IAM）** "。 
    1. 在工具栏上选择 " **+ 添加**"，然后选择 "**添加角色分配**"。  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="添加角色管理-菜单":::
    1. 选择允许进行更多访问的 "**读取**者" 角色或角色。 
    1. 键入要为其使用磁盘加密集的实验室名称，并从下拉列表中选择实验室名称（实验室的系统分配的标识）。 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="选择实验室的系统管理的标识":::        
    1. 在工具栏上选择“保存”。 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="保存角色分配":::
## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>使用客户管理的密钥加密实验室 OS 磁盘 

1. 在 Azure 门户的实验室主页上，选择左侧菜单中的 "**配置和策略**"。 
1. 在 "**配置和策略**" 页上，选择 "**加密**" 部分中的 "**磁盘（预览版）** "。 默认情况下，**使用平台托管密钥**将**加密类型**设置为 "静态加密"。

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text=""配置和策略" 页的 "磁盘" 选项卡":::
1. 对于 "**加密类型**"，请从下拉列表中选择 "静态加密"，并选择 "**客户管理的密钥**"。 
1. 对于 "**磁盘加密集**"，请选择之前创建的磁盘加密集。 它与系统分配的实验室标识可访问的磁盘加密集相同。
1. 在工具栏上选择“保存”。 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="使用客户管理的密钥启用加密":::
1. 在包含以下文本的消息框上：*此设置将应用于实验室中新创建的计算机。旧操作系统磁盘将在旧的磁盘加密集中保持加密*，请选择 **"确定"**。 

    配置完成后，将使用磁盘加密集提供的客户托管密钥来加密实验室操作系统磁盘。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [Azure 磁盘加密](../virtual-machines/windows/disk-encryption.md)。 
- [客户管理的密钥](../virtual-machines/windows/disk-encryption.md#customer-managed-keys) 