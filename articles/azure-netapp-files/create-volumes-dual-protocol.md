---
title: 为 Azure NetApp 文件创建双重协议（NFSv3 和 SMB） |Microsoft Docs
description: 介绍如何创建使用 NFSv3 和 SMB 双重协议的卷，并支持 LDAP 用户映射。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/28/2020
ms.author: b-juche
ms.openlocfilehash: 61e8c56e75e82bc28ddb2abf231d9a5e919691b0
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535344"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>为 Azure NetApp 文件创建双重协议（NFSv3 和 SMB）卷

Azure NetApp 文件支持使用 NFS （NFSv3 和 NFSv 4.1）、SMBv3 或双重协议创建卷。 本文介绍如何创建使用 NFSv3 和 SMB 的双重协议的卷，并支持 LDAP 用户映射。  


## <a name="before-you-begin"></a>开始之前 

* 必须已设置容量池。  
    请参阅[设置容量池](azure-netapp-files-set-up-capacity-pool.md)。   
* 子网必须委派给 Azure NetApp 文件。  
    请参阅[向 Azure NetApp 文件委托子网](azure-netapp-files-delegate-subnet.md)。

## <a name="considerations"></a>注意事项

* 确保满足[Active Directory 连接的要求](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)。 
* 确保 NFS 客户端是最新的，并且运行最新的操作系统更新。

## <a name="create-a-dual-protocol-volume"></a>创建双协议卷

1.  从“容量池”边栏选项卡中单击“卷”边栏选项卡。 单击“+ 添加卷”以创建卷。 

    ![导航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  在 "创建卷" 窗口中，单击 "**创建**"，并在 "基本信息" 选项卡下提供以下字段的信息：   
    * **卷名称**      
        指定要创建的卷的名称。   

        卷名称在每个容量池中必须是唯一的。 它的长度必须至少为三个字符。 你可以使用任何字母数字字符。   

        不能将 `default` 用作卷名。

    * **容量池**  
        指定要在其中创建卷的容量池。

    * **配额**  
        指定分配给卷的逻辑存储量。  

        “可用配额”字段显示了所选容量池中可以用来创建新卷的未使用空间量。 新卷的大小不能超过可用配额。  

    * **虚拟网络**  
        指定要从中访问卷的 Azure 虚拟网络 (VNet)。  

        你指定的 Vnet 必须已将子网委托给 Azure NetApp 文件。 只能从同一 Vnet 或者从与卷位于同一区域的 Vnet 通过 Vnet 对等互连来访问 Azure NetApp 文件服务。 还可以从本地网络通过 Express Route 来访问卷。   

    * **子网**  
        指定要用于卷的子网。  
        你指定的子网必须委派给 Azure NetApp 文件。 
        
        如果尚未委派子网，可以在“创建卷”页面上单击“新建”。**** 然后，在“创建子网”页面中，指定子网信息，并选择“Microsoft.NetApp/卷”来为 Azure NetApp 文件委派子网。 在每个 Vnet 中，只能将一个子网委托给 Azure NetApp 文件。   
 
        ![创建卷](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![创建子网](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 如果要将现有的快照策略应用到卷，请单击 "**显示高级" 部分**将其展开，然后在下拉菜单中选择一个快照策略。 

        有关创建快照策略的信息，请参阅[管理快照策略](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)。

        ![显示高级选择](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. 单击“协议”****，然后完成以下操作：  
    * 选择 "**双重协议" （NFSv3 和 SMB）** 作为卷的协议类型。   

    * 从下拉列表中选择 " **Active Directory**连接"。  
    使用的 Active Directory 必须具有服务器根 CA 证书。 

    * 指定卷的**卷路径**。   
    此卷路径是共享卷的名称。 名称必须以字母字符开头，并且在每个订阅和每个区域中必须是唯一的。  

    * 指定要使用的**安全样式**： NTFS （默认值）或 UNIX。

    * （可选）[配置卷的导出策略](azure-netapp-files-configure-export-policy.md)。

    ![指定双重协议](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. 单击“查看 + 创建”以查看卷详细信息。 然后单击 "**创建**" 来创建卷。

    创建的卷将显示在“卷”页中。 
 
    卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>上载 Active Directory 证书颁发机构公共根证书  

1.  按照[安装证书颁发机构的](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority)步骤安装和配置添加证书颁发机构。 

2.  遵循使用[mmc 管理单元查看证书](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)，使用 mmc 管理单元和证书管理器工具。  
    使用 "证书管理器" 管理单元查找本地设备的根证书或证书证书。 你应从下列设置之一运行证书管理管理单元命令：  
    * 已加入域并已安装根证书的基于 Windows 的客户端 
    * 包含根证书的域中的另一台计算机  

3. 导出根证书。  
    确保将证书导出为64编码的 x.509 （。CER）格式： 

    ![证书导出向导](../media/azure-netapp-files/certificate-export-wizard.png)

4. 中转到双重协议卷的 NetApp 帐户，单击**Active Directory 连接**"，然后使用"**加入 Active Directory** "窗口上传根 CA 证书：  

    ![服务器根 CA 证书](../media/azure-netapp-files/server-root-ca-certificate.png)

    确保证书颁发机构名称可以通过 DNS 进行解析。 此名称是证书上的 "颁发者" 或 "颁发者" 字段：  

    ![证书信息](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>管理 LDAP POSIX 属性

可以通过使用 "Active Directory 用户和计算机" MMC 管理单元来管理 POSIX 属性，如 UID、Home 目录和其他值。  下面的示例演示 Active Directory 特性编辑器：  

![Active Directory 特性编辑器](../media/azure-netapp-files/active-directory-attribute-editor.png) 


## <a name="configure-the-nfs-client"></a>配置 NFS 客户端 

按照为[Azure NetApp 文件配置 nfs 客户端](configure-nfs-clients.md)中的说明配置 nfs 客户端。  

## <a name="next-steps"></a>后续步骤  

* [双重协议常见问题](azure-netapp-files-faqs.md#dual-protocol-faqs)
* [为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md) 
