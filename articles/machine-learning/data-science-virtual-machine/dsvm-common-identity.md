---
title: 设置数据科学虚拟机的通用标识 - Azure | Microsoft Docs
description: 在企业团队的 DSVM 环境中设置通用标识。
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析, 团队数据科学过程
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 70c6b8cd147cefaa3128bc1e6a414a6fa61dba6d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830888"
---
# <a name="setup-common-identity-on-the-data-science-vm"></a>在数据科学 VM 上设置通用标识

默认情况下，在包括数据科学 VM (DSVM) 在内的 Azure VM 上，会在预配 VM 时创建本地用户帐户，这些用户使用这些凭据向 VM 进行身份验证。 如果需要访问多个 VM，使用此方法来管理凭据可能会很快变得十分麻烦。 通用用户帐户和管理使用基于标准的标识提供者，允许你通过一组凭据来访问 Azure 上的多个资源（包括多个 DSVM）。 

Active Directory (AD) 是常用的标识提供者，可以在 Azure 上充当服务，也可以在本地使用。 可以利用 AD 或 Azure AD，在单独的数据科学 VM (DSVM) 或 Azure 虚拟机规模集的 DSVM 群集上对用户进行身份验证。 为此，可以将 DSVM 实例加入 AD 域。 如果已经有一个用来管理标识的 Active Directory，可以将它用作通用标识提供者。 如果没有 AD，可以通过名为 [Azure Active Directory 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS) 的服务在 Azure 上运行托管的 AD。 

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/) 的文档提供了托管 Active Directory 的详细[说明](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity)，包括如何将 Azure AD 连接到本地目录（如果有）。 

本文其余部分介绍的步骤说明了如何在 Azure 上使用 Azure AD DS 设置完全托管的 AD 域服务，以及如何将 DSVM 加入托管的 AD 域，以便用户使用通用用户帐户和凭据访问 DSVM（和其他 Azure 资源）的池。 

##  <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>在 Azure 上设置完全托管的 Active Directory 域

Azure AD DS 在 Azure 上提供完全托管的服务，简化了标识管理。 在此 Active Directory 域上，可以管理用户和组。  若要在目录中设置 Azure 托管的 AD 域和用户帐户，可以执行以下步骤：

1. 在门户中将用户添加到 Active Directory 

    a. 使用属于目录全局管理员的帐户登录到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
    
    b. 选择“Azure Active Directory”，然后选择“用户和组”。
    
    c. 在“用户和组”中，选择“所有用户”，然后选择“新建用户”。
   
   ![选择“添加”命令](./media/add-user.png)
    
    d. 输入用户的详细信息，如**名称**和**用户名**。 用户名的域名部分必须是初始默认域名“[domain name].onmicrosoft.com”，或已验证的非联合[自定义域名](../../active-directory/add-custom-domain.md)（例如“contoso.com”）。
    
    e. 复制或以其他方式记下生成的用户密码，以便在此过程完成后可以提供给用户。
    
    f. （可选）可以打开“个人资料”、“组”或“目录角色”并在其中填写用户信息。 
    
    g. 在“用户”上，选择“创建”。
    
    h. 以安全方式将生成的密码分发给新用户，以便用户可以登录。

2.  创建 Azure AD 域服务

    若要创建 Azure ADDS，请按“[使用 Azure 门户启用 Azure Active Directory 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)”一文（任务 1 到任务 5）中的说明操作。 必须更新 Active Directory 中的现有用户密码，以便同步 Azure AD DS 中的密码。 此外还必须将 DNS 添加到 Azure AD DS，如上文的任务 4 中列出的那样。 

3.  在上一步骤的任务 2 所创建的虚拟网络中创建单独的 DSVM 子网
4.  在 DSVM 子网中创建一个或多个数据科学 VM 实例 
5.  按[说明](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm )将 DSVM 添加到 AD。 
6.  接下来，请装载用于托管主目录或 Notebook 目录的共享 Azure 文件，以便在任何计算机上装载工作区。 （如果需要严格的文件级别权限，则需在一个或多个 VM 上运行 NFS）

    a. [创建 Azure 文件共享](../../storage/files/storage-how-to-create-file-share.md)
    
    b. 将其装载到 Linux DSVM 上。 在 Azure 门户的存储帐户中单击 Azure 文件所对应的“连接”按钮时，将会显示可以在 Linux DSVM 的 Bash Shell 中运行的命令。 命令将如下所示：
```
sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
```
7.  假设已将 Azure 文件装载在 /data/workspace 中。 现在，请为共享中的每个用户创建目录。 /data/workspace/user1、/data/workspace/user2，等等。 在每个用户的工作区中创建 ```notebooks``` 目录。 
8. 在 ```$HOME/userx/notebooks/remote``` 中为 ```notebooks``` 创建符号链接。   

现在，你在托管在 Azure 中的 Active Directory 中有了用户，并且能够登录到可以使用 AD 凭据加入 Azure AD DS 的任何 DSVM（SSH 和 Jupyterhub）。 由于用户工作区位于共享 Azure 文件上，因此用户在使用 Jupyterhub 时，可以访问其 Notebook 以及任何 DSVM 中的其他工作。 

若要进行自动缩放，可以使用虚拟机规模集创建 VM 池。这些 VM 全都以这种方式加入域，并且已装载了共享磁盘。 用户可以登录到虚拟机规模集中的任何可用计算机，然后访问在其中保存了 Notebook 的共享磁盘。 

## <a name="next-steps"></a>后续步骤

* [以安全方式存储访问云资源的凭据](dsvm-secure-access-keys.md)



