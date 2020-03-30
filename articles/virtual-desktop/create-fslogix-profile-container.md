---
title: FSLogix 配置文件容器 NetApp Windows 虚拟桌面 - Azure
description: 如何使用 Windows 虚拟桌面中的 Azure NetApp 文件创建 FSLogix 配置文件容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 272188b50fe59435031a4a2fb9c252f3f358bb6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535725"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>使用 Azure NetApp 文件为主机池创建 FSLogix 配置文件容器

我们建议使用 FSLogix 配置文件容器作为[Windows 虚拟桌面服务](overview.md)的用户配置文件解决方案。 FSLogix 配置文件容器将完整的用户配置文件存储在单个容器中，并设计用于在非持久性远程计算环境中（如 Windows 虚拟桌面）中漫游配置文件。 登录时，容器使用本地支持的虚拟硬盘 （VHD） 和超 V 虚拟硬盘 （VHDX） 动态附加到计算环境。 这些高级筛选器驱动程序技术允许用户配置文件立即可用，并完全像本地用户配置文件一样显示在系统中。 要了解有关 FSLogix 配置文件容器的更多内容，请参阅[FSLogix 配置文件容器和 Azure 文件](fslogix-containers-azure-files.md)。

您可以使用[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)创建 FSLogix 配置文件容器，这是一种易于使用的 Azure 本机平台服务，可帮助客户快速可靠地为其 Windows 虚拟桌面环境预配企业级 SMB 卷。 要了解有关 Azure NetApp 文件的更多内容，请参阅[什么是 Azure NetApp 文件？](../azure-netapp-files/azure-netapp-files-introduction.md)

本指南将介绍如何设置 Azure NetApp 文件帐户，并在 Windows 虚拟桌面中创建 FSLogix 配置文件容器。

本文假定您已经在 Windows 虚拟桌面环境中设置[主机池](create-host-pools-azure-marketplace.md)并分组到一个或多个租户中。 要了解如何设置租户，请参阅[在 Windows 虚拟桌面中创建租户](tenant-setup-azure-active-directory.md)和[我们的技术社区博客文章](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)。

本指南中的说明是专门针对 Windows 虚拟桌面用户的。 如果要查找有关如何在 Windows 虚拟桌面之外设置 Azure NetApp 文件和创建 FSLogix 配置文件容器的更常规指南，请参阅[设置 Azure NetApp 文件并创建 NFS 卷快速启动](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)。

>[!NOTE]
>本文不介绍保护对 Azure NetApp 文件共享的访问的最佳做法。

>[!NOTE]
>如果要查找有关 Azure 上不同 FSLogix 配置文件容器存储选项的比较材料，请参阅[FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)。

## <a name="prerequisites"></a>先决条件

在为主机池创建 FSLogix 配置文件容器之前，必须：

- 设置和配置 Windows 虚拟桌面
- 预配 Windows 虚拟桌面主机池
- [启用 Azure NetApp 文件订阅](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>设置 Azure NetApp 文件帐户

要开始，您需要设置 Azure NetApp 文件帐户。

1. 登录到 Azure[门户](https://portal.azure.com)。 确保您的帐户具有参与者或管理员权限。

2. 选择搜索栏右侧的**Azure 云外壳图标**以打开 Azure 云外壳。

3. 打开 Azure 云外壳后，选择**PowerShell**。

4. 如果这是您第一次使用 Azure 云外壳，请在同一订阅中创建存储帐户，以保留 Azure NetApp 文件和 Windows 虚拟桌面。

   ![窗口底部的创建存储按钮以红色突出显示的存储帐户窗口。](media/create-storage-button.png)

5. 加载 Azure 云外壳后，运行以下两个 cmdlet。

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. 在窗口的左侧，选择 **"所有服务**"。 在菜单顶部显示的搜索框中输入**Azure NetApp 文件**。

   ![在"所有服务"搜索框中输入"Azure NetApp 文件"的用户的屏幕截图。 搜索结果显示 Azure NetApp 文件资源。](media/azure-netapp-files-search-box.png)


7. 在搜索结果中选择**Azure NetApp 文件**，然后选择 **"创建**"。

8. 选择"**添加**"按钮。
9. 打开 **"新建 NetApp 帐户**"选项卡时，输入以下值：

    - 对于**名称**，请输入您的 NetApp 帐户名称。
    - 对于**订阅**，从下拉菜单中选择您在步骤 4 中设置的存储帐户的订阅。
    - 对于**资源组**，从下拉菜单中选择现有资源组，或者通过选择 **"创建新**"来创建新资源组。
    - 对于**位置**，从下拉菜单中选择 NetApp 帐户的区域。 此区域必须与会话主机 VM 的区域相同。

   >[!NOTE]
   >Azure NetApp 文件当前不支持跨区域安装卷。

10. 完成后，选择 **"创建**"以创建 NetApp 帐户。

## <a name="create-a-capacity-pool"></a>创建容量池

接下来，创建新的容量池： 

1. 转到 Azure NetApp 文件菜单并选择新帐户。
2. 在帐户菜单中，选择"存储服务"下的**容量池**。
3. 选择 **"添加池**"。
4. 打开 **"新建容量池**"选项卡时，输入以下值：

    - 对于**名称**，输入新容量池的名称。
    - 对于**服务级别**，从下拉菜单中选择所需的值。 我们建议大多数环境**都提供高级版**。
       >[!NOTE]
       >"高级"设置提供高级服务级别（256 MBps）的最小吞吐量。 您可能需要为生产环境调整此吞吐量。 最终吞吐量基于[吞吐量限制](../azure-netapp-files/azure-netapp-files-service-levels.md)中描述的关系。
    - 对于**大小 （TiB），** 输入最适合您需求的容量池大小。 最小尺寸为 4 TiB。

5. 完成后，选择“确定”****。

## <a name="join-an-active-directory-connection"></a>加入活动目录连接

之后，您需要加入活动目录连接。

1. 在页面左侧的菜单中选择 **"活动目录"连接**，然后选择 **"加入**"按钮以打开 **"加入活动目录"** 页。

   ![加入活动目录连接菜单的屏幕截图。](media/active-directory-connections-menu.png)

2. 在 **"加入活动目录"** 页中输入以下值以加入连接：

    - 对于**主 DNS，** 输入环境中可解析域名的 DNS 服务器的 IP 地址。
    - 对于**域**，输入完全限定的域名 （FQDN）。
    - 对于**SMB 服务器（计算机帐户）前缀**，输入要追加到计算机帐户名称的字符串。
    - 对于**用户名**，输入具有执行域联接权限的帐户的名称。
    - 对于**密码**，输入帐户的密码。

  >[!NOTE]
  >最佳做法是确认您在[加入活动目录连接](create-fslogix-profile-container.md#join-an-active-directory-connection)中创建的计算机帐户已出现在**您的计算机**或**企业的相关 OU**下的域控制器中。

## <a name="create-a-new-volume"></a>创建新卷

接下来，您需要创建新卷。

1. 选择 **"卷**"，然后选择 **"添加卷**"。

2. 打开"**创建卷**"选项卡时，输入以下值：

    - 对于**卷名称**，输入新卷的名称。
    - 对于**容量池**，从下拉菜单中选择刚刚创建的容量池。
    - 对于**配额 （GiB），** 输入适合您的环境的卷大小。
    - 对于**虚拟网络**，从下拉菜单中选择与域控制器连接的现有虚拟网络。
    - 在**子网**下，选择 **"创建新**"。 请记住，此子网将委派给 Azure NetApp 文件。

3.  选择 **"下一\>步：协议**"以打开"协议"选项卡并配置卷访问参数。

## <a name="configure-volume-access-parameters"></a>配置卷访问参数

创建卷后，配置卷访问参数。

1.  选择**SMB**作为协议类型。
2.  在 **"活动目录**"下拉菜单中的"配置"下，选择最初在["加入活动目录"连接](create-fslogix-profile-container.md#join-an-active-directory-connection)中连接的同一目录。 请记住，每个订阅有一个活动目录的限制。
3.  在 **"共享名称**"文本框中，输入会话主机池及其用户使用的共享的名称。

4.  选择 **"查看 + 在**页面底部创建"。 这将打开验证页。 成功验证卷后，选择 **"创建**"。

5.  此时，新卷将开始部署。 部署完成后，可以使用 Azure NetApp 文件共享。

6.  要查看装载路径，请选择 **"转到资源"** 并在"概述"选项卡中查找它。

    !["概述"屏幕的屏幕截图，红色箭头指向装载路径。](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>在会话主机虚拟机 （VM） 上配置 FSLogix

本节基于[使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。

1. [下载 FSLogix 代理 .zip 文件](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409)，而您仍在会话主机 VM 中远程。

2. 解压缩下载的文件。

3. 在文件中，转到**x64** > **版本**并运行**FSLogixAppsSetup.exe**。 安装菜单将打开。

4.  如果您有产品密钥，请在"产品密钥"文本框中输入它。

5. 选中**我同意许可条款和条件**旁边的复选框。

6. 选择**安装**。

7. 导航到**\\C：程序\\文件\\FSLogix 应用**以确认已安装的代理。

8. 从"开始"菜单中，以管理员身份运行**RegEdit。**

9. 导航到**计算机\\HKEY_LOCAL_MACHINE\\软件\\FSLogix。**

10. 创建名为**配置文件**的键。

11.  创建名为"**已启用"** 的值 **，REG_DWORD**类型设置为**1**。

12. 使用**多字符串**类型创建名为**VHDData 的值**，并将其数据值设置为 Azure NetApp 文件共享的 URI。

13. 创建名为 **"删除本地配置文件时VHD应应用**"的值，DWORD 值为 1，以避免在登录前出现现有本地配置文件的问题。

     >[!WARNING]
     >创建"删除本地配置文件时应应用"值时要小心。 当 FSLogix 配置文件系统确定用户应具有 FSLogix 配置文件，但本地配置文件已存在时，配置文件容器将永久删除本地配置文件。 然后，用户将使用新的 FSLogix 配置文件登录。

## <a name="assign-users-to-session-host"></a>将用户分配给会话主机

1. 以管理员身份打开**PowerShell ISE**并登录到 Windows 虚拟桌面。

2. 运行以下 cmdlet：

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. 当提示您输入凭据时，在 Windows 虚拟桌面租户上输入具有租户创建者或 RDS 所有者/RDS 参与者角色的用户的凭据。

4. 运行以下 cmdlet 将用户分配给远程桌面组：

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>确保用户可以访问 Azure NetApp 文件共享

1. 打开您的互联网浏览器，然后转到<https://rdweb.wvd.microsoft.com/webclient/index.html>。

2. 使用分配给远程桌面组的用户的凭据登录。

3. 建立用户会话后，使用管理帐户登录到 Azure 门户。

4. 打开**Azure NetApp 文件**，选择 Azure NetApp 文件帐户，然后选择 **"卷**"。 打开"卷"菜单后，选择相应的卷。

   ![在 Azure 门户中设置的 NetApp 帐户的屏幕截图，选中"卷"按钮。](media/netapp-account.png)

5. 转到 **"概述"** 选项卡，确认 FSLogix 配置文件容器正在使用空间。

6. 使用远程桌面直接连接到主机池的任何 VM 部分，然后打开**文件资源管理器。** 然后导航到**装载路径**（在下面的示例中，装载路径anf-SMB-3863.gt1107.onmicrosoft.com \\ \\\\af-VOL）。

   在此文件夹中，应该有类似于以下示例中的配置文件 VHD（或 VHDX）。

   ![装载路径中文件夹内容的屏幕截图。 里面是一个名为"Profile_ssbb"的 VHD 文件。](media/mount-path-folder.png)

## <a name="next-steps"></a>后续步骤

您可以使用 FSLogix 配置文件容器来设置用户配置文件共享。 要了解如何使用新容器创建用户配置文件共享，请参阅[使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。
