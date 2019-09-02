---
title: 设置公共标识
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何创建可跨多个 Data Science Virtual Machine 使用的公共用户帐户。 可以使用 Azure Active Directory 或本地 Active Directory 对 Data Science Virtual Machine 的用户进行身份验证。
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析, 团队数据科学过程
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208143"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>在 Data Science Virtual Machine 上设置公共标识

在 Microsoft Azure 虚拟机 (VM) (包括 Data Science Virtual Machine (DSVM)) 上, 在预配 VM 时创建本地用户帐户。 然后，用户使用这些凭据对 VM 进行身份验证。 如果你有多个用户需要访问的 Vm, 则管理凭据可能会非常麻烦。 一个优秀的解决方案是通过基于标准的标识提供者部署常见用户帐户和管理。 通过此方法, 你可以使用一组凭据来访问 Azure 上的多个资源, 包括多个 Dsvm。

Active Directory 是一种常用的标识提供者, 在 Azure 上作为云服务和本地目录支持。 你可以使用 Azure Active Directory (Azure AD) 或本地 Active Directory 对 Azure 虚拟机规模集中的独立 DSVM 或 DSVM 群集上的用户进行身份验证。 你可以通过将 DSVM 实例加入 Active Directory 域来完成此操作。

如果你已经有 Active Directory, 则可以将其用作你的公共标识提供者。 如果没有 Active Directory, 则可以通过[Azure Active Directory 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/)(Azure AD DS) 在 Azure 上运行托管的 Active Directory 实例。

[Azure AD](https://docs.microsoft.com/azure/active-directory/)的文档提供了详细的[管理说明](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution), 其中包括有关将 Azure AD 连接到本地目录 (如果有) 的指导。

本文介绍如何使用 Azure AD DS 在 Azure 上设置完全托管的 Active Directory 域服务。 然后, 可以将 Dsvm 加入托管 Active Directory 域。 此方法使用户能够通过通用用户帐户和凭据访问 Dsvm (和其他 Azure 资源) 池。

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>在 Azure 上设置完全托管的 Active Directory 域

Azure AD DS 在 Azure 上提供完全托管的服务，简化了标识管理。 在此 Active Directory 域上，可以管理用户和组。 若要设置 Azure 托管 Active Directory 域和目录中的用户帐户, 请执行以下步骤:

1. 在 Azure 门户中，将用户添加到 Active Directory： 

   1. 使用该目录的全局管理员帐户登录到[Azure Active Directory 管理中心](https://aad.portal.azure.com)。
    
   1. 选择“Azure Active Directory”，然后选择“用户和组”。
    
   1. 在 "**用户和组**" 中, 选择 "**所有用户**", 然后选择 "**新建用户**"。
   
           The **User** pane opens:
      
      ![“用户”窗格](./media/add-user.png)
    
   1. 输入用户的详细信息，如**名称**和**用户名**。 用户名的域名部分必须是初始默认域名“[domain name].onmicrosoft.com”，或已验证的非联合[自定义域名](../../active-directory/add-custom-domain.md)（例如“contoso.com”）。
    
   1. 复制或以其他方式记下生成的用户密码，以便在此过程完成后可以提供给用户。
    
   1. （可选）可以打开“个人资料”、“组”或“目录角色”并在其中填写用户信息。 
    
   1. 在 "**用户**" 下, 选择 "**创建**"。
    
   1. 安全地将生成的密码分发给新用户, 以便用户可以登录。

1. 创建 Azure AD DS 实例。 按照[使用 Azure 门户启用 Azure Active Directory 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)("创建实例和配置基本设置" 部分) 中的说明进行操作。 必须更新 Active Directory 中的现有用户密码，以便同步 Azure AD DS 中的密码。 将 DNS 添加到 Azure AD DS 也很重要, 如在该部分中的 "完成 Azure AD Azure 门户的基本窗口中的" 基本 "窗口中所述。

1. 在上一步的 "创建和配置虚拟网络" 一节中创建的虚拟网络中创建单独的 DSVM 子网。
1. 在 DSVM 子网中创建一个或多个 DSVM 实例。
1. 按照[说明](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm )将 DSVM 添加到 Active Directory。 
1. 装载 Azure 文件共享以托管你的家庭或笔记本目录, 以便可以将你的工作区装载到任何计算机上。 (如果需要严格的文件级权限, 则需要在一个或多个 Vm 上运行网络文件系统 [NFS]。)

   1. [创建 Azure 文件共享](../../storage/files/storage-how-to-create-file-share.md)。
    
   2.  在 Linux DSVM 上装载此共享。 为 Azure 门户中的存储帐户中的 Azure 文件共享选择 "**连接**" 时, 将显示在 Linux DSVM 上的 bash shell 中运行的命令。 命令如下所示：
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. 例如, 假设你已在/data/workspace. 中装载 Azure 文件共享。 现在, 请为共享中的每个用户创建目录:/data/workspace/user1、/data/workspace/user2 等。 在每个用户的工作区中创建 `notebooks` 目录。 
1. 在 `$HOME/userx/notebooks/remote` 中为 `notebooks` 创建符号链接。   

你现在已在 Azure 中托管 Active Directory 实例中的用户。 通过使用 Active Directory 凭据, 用户可以登录到已加入到 Azure AD DS 的任何 DSVM (SSH 或 JupyterHub)。 由于用户工作区位于共享 Azure 文件上，因此用户在使用 JupyterHub 时，可以访问其笔记本以及 DSVM 中的任何其他工作。

若要进行自动缩放，可以使用虚拟机规模集创建 VM 池。这些 VM 全都以这种方式加入域，并且已装载了共享磁盘。 用户可以登录到虚拟机规模集中的任何可用计算机, 并且可以访问其笔记本保存到的共享磁盘。 

## <a name="next-steps"></a>后续步骤

* [以安全方式存储访问云资源的凭据](dsvm-secure-access-keys.md)



