---
title: FSLogix 配置文件容器 NetApp Windows 虚拟桌面-Azure
description: 如何使用 Windows 虚拟桌面中的 Azure NetApp 文件创建 FSLogix 配置文件容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 294a59ed94344ecf590eb9b34f991deaaa10db69
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607378"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>使用 Azure NetApp 文件为主机池创建 FSLogix 配置文件容器

建议使用 FSLogix 配置文件容器作为[Windows 虚拟桌面服务](overview.md)的用户配置文件解决方案。 FSLogix 配置文件容器将完整的用户配置文件存储在单个容器中，并设计为在非持久性远程计算环境（如 Windows 虚拟桌面）中漫游配置文件。 登录时，容器会使用本地支持的虚拟硬盘（VHD）和 Hyper-v 虚拟硬盘（VHDX）以动态方式附加到计算环境。 这些高级筛选器驱动程序技术允许用户配置文件立即可用，并与本地用户配置文件完全相同。 若要了解有关 FSLogix 配置文件容器的详细信息，请参阅[FSLogix 配置文件容器和 Azure 文件](fslogix-containers-azure-files.md)。

可以使用[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)创建 FSLogix 配置文件容器，这是一种易于使用的 azure 本机平台服务，可帮助客户快速、可靠地为其 Windows 虚拟桌面环境设置企业级 SMB 卷。 若要了解有关 Azure NetApp 文件的详细信息，请参阅[什么是 Azure Netapp 文件？](../azure-netapp-files/azure-netapp-files-introduction.md)

本指南将演示如何设置 Azure NetApp 文件帐户，以及如何在 Windows 虚拟桌面中创建 FSLogix 配置文件容器。

本文假设已在 Windows 虚拟桌面环境中设置了[主机池](create-host-pools-azure-marketplace.md)并将其分组到一个或多个租户。 若要了解如何设置租户，请参阅[在 Windows 虚拟桌面中创建租户](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)和[我们的技术社区博客文章](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)。

本指南中的说明专门针对 Windows 虚拟桌面用户。 如果要了解有关如何在 Windows 虚拟桌面之外设置 Azure NetApp 文件和创建 FSLogix 配置文件容器的更多常规指导，请参阅[设置 Azure Netapp 文件和创建 NFS 卷快速入门](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)。

>[!NOTE]
>本文不介绍用于保护对 Azure NetApp 文件共享的访问的最佳做法。

>[!NOTE]
>如果正在查找有关 Azure 上不同 FSLogix 配置文件容器存储选项的比较资料，请参阅[FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)。

## <a name="prerequisites"></a>先决条件

必须先执行以下操作，然后才能为主机池创建 FSLogix 配置文件容器：

- 设置和配置 Windows 虚拟桌面
- 预配 Windows 虚拟桌面主机池
- [启用 Azure NetApp 文件订阅](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>设置你的 Azure NetApp 文件帐户

若要开始，需要设置 Azure NetApp 文件帐户。

1. 登录 [Azure 门户](https://portal.azure.com)。 请确保你的帐户具有参与者或管理员权限。

2. 选择搜索栏右侧的 " **Azure Cloud Shell" 图标**以打开 Azure Cloud Shell。

3. 打开 Azure Cloud Shell 后，请选择 " **PowerShell**"。

4. 如果这是你第一次使用 Azure Cloud Shell，请在同一订阅中创建存储帐户，以保留你的 Azure NetApp 文件和 Windows 虚拟桌面。

   ![位于窗口底部的 "创建存储" 按钮的 "存储帐户" 窗口以红色突出显示。](media/create-storage-button.png)

5. Azure Cloud Shell 加载后，运行以下两个 cmdlet。

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. 在窗口左侧，选择 "**所有服务**"。 将**Azure NetApp 文件**输入到菜单顶部显示的 "搜索" 框中。

   ![用户在 "所有服务" 搜索框中输入 "Azure NetApp 文件" 的屏幕截图。 搜索结果显示 "Azure NetApp 文件" 资源。](media/azure-netapp-files-search-box.png)


7. 在搜索结果中选择 " **Azure NetApp 文件**"，然后选择 "**创建**"。

8. 选择“添加”按钮。 
9. 当 "**新 NetApp 帐户**" 选项卡打开时，输入以下值：

    - 对于 "**名称**"，请输入你的 NetApp 帐户名称。
    - 对于 "**订阅**"，从下拉菜单中选择你在步骤4中设置的存储帐户的订阅。
    - 对于 "**资源组**"，请从下拉菜单中选择现有的资源组，或通过选择 **"新建"** 创建一个新的资源组。
    - 对于 "**位置**"，请从下拉菜单中选择你的 NetApp 帐户所在的区域。 此区域必须与你的会话主机 Vm 位于同一区域。

   >[!NOTE]
   >Azure NetApp 文件当前不支持跨区域装载卷。

10. 完成后，选择 "**创建**" 以创建 NetApp 帐户。

## <a name="create-a-capacity-pool"></a>创建容量池

接下来，创建新的容量池： 

1. 请在 "Azure NetApp 文件" 菜单中，选择新的帐户。
2. 在帐户菜单中，选择 "存储服务" 下的**容量池**。
3. 选择 "**添加池**"。
4. 打开 "**新建容量池**" 选项卡时，输入以下值：

    - 对于 "**名称**"，请输入新容量池的名称。
    - 对于 "**服务级别**"，请从下拉菜单中选择所需的值。 对于大多数环境，建议**高级**。
       >[!NOTE]
       >"高级" 设置提供适用于高级服务级别（256 MBps）的最小吞吐量。 对于生产环境，可能需要调整此吞吐量。 最终吞吐量基于[吞吐量限制](../azure-netapp-files/azure-netapp-files-service-levels.md)中所述的关系。
    - 对于 "**大小（TiB）**"，输入最适合你的需求的容量池大小。 最小大小为 4 TiB。

5. 完成后，选择“确定”****。

## <a name="join-an-active-directory-connection"></a>联接 Active Directory 连接

然后，需要加入 Active Directory 连接。

1. 在页面左侧的菜单中选择 " **Active Directory 连接**"，然后选择 "**联接**" 按钮打开 "**联接 Active Directory** " 页。

   ![联接 Active Directory 连接 "菜单的屏幕截图。](media/active-directory-connections-menu.png)

2. 在 "**联接 Active Directory** " 页中输入以下值以联接连接：

    - 对于**主 dns**，请在你的环境中输入可以解析域名的 DNS 服务器的 IP 地址。
    - 对于 "**域**"，输入完全限定的域名（FQDN）。
    - 对于 " **SMB 服务器（计算机帐户）" 前缀**，请输入要追加到计算机帐户名称的字符串。
    - 对于 "**用户名**"，请输入具有执行域加入权限的帐户的名称。
    - 对于 "**密码**"，输入帐户的密码。

## <a name="create-a-new-volume"></a>创建新卷

接下来，需要创建一个新卷。

1. 选择**卷**，然后选择 "**添加卷**"。

2. 打开 "**创建卷**" 选项卡时，输入以下值：

    - 对于 "**卷名称**"，请输入新卷的名称。
    - 对于 "**容量池**"，请从下拉菜单中选择刚创建的容量池。
    - 对于 "**配额（GiB）**"，输入适合你的环境的卷大小。
    - 对于 "**虚拟网络**"，请从下拉菜单中选择一个已连接到域控制器的现有虚拟网络。
    - 在 "**子网**" 下，选择 "**新建**"。 请记住，此子网将委托给 Azure NetApp 文件。

3.  选择 "**下一\>步：协议**"，打开 "协议" 选项卡并配置卷访问参数。

## <a name="configure-volume-access-parameters"></a>配置卷访问参数

创建卷后，请配置卷访问参数。

1.  选择**SMB**作为协议类型。
2.  在 "配置" 下的 " **Active Directory** " 下拉菜单中，选择最初连接的同一目录 "[加入 Active Directory 连接](create-fslogix-profile-container.md#join-an-active-directory-connection)"。 请记住，每个订阅有一个 Active Directory 限制。
3.  在 "**共享名**" 文本框中，输入会话主机池及其用户使用的共享的名称。

4.  在页面底部选择“查看 + 创建”  。 这将打开 "验证" 页。 成功验证卷后，选择 "**创建**"。

5.  此时，新卷将开始部署。 部署完成后，可以使用 Azure NetApp 文件共享。

6.  若要查看装载路径，请选择 "**中转到资源**"，然后在 "概述" 选项卡中查找它。

    !["概述" 屏幕的屏幕截图，其中包含一个指向装入路径的红色箭头。](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>在会话主机虚拟机（Vm）上配置 FSLogix

本部分基于[使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。

1. [下载 FSLogix agent .zip 文件](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409)，但仍在会话主机 VM 中进行远程处理。

2. 解压缩下载的文件。

3. 在文件中，中转到**x64** > **版本**并运行**FSLogixAppsSetup**。 将打开安装菜单。

4.  如果有产品密钥，请在 "产品密钥" 文本框中输入该密钥。

5. 选中 "**我同意许可条款和条件**" 旁边的复选框。

6. 选择“安装”  。

7. 导航到**C：\\Program Files\\FSLogix\\Apps**以确认已安装代理。

8. 从 "开始" 菜单中，以管理员身份运行**RegEdit** 。

9. 导航到 **"\\计算机\\HKEY_LOCAL_MACHINE\\software FSLogix**"。

10. 创建名为 "**配置文件**" 的密钥。

11.  使用设置为数据值**1**的**REG_DWORD**类型创建名为**Enabled**的值。

12. 使用**多字符串**类型创建名为**VHDLocations**的值，并将其数据值设置为 Azure NETAPP 文件共享的 URI。

13. 创建一个名为**DeleteLocalProfileWhenVHDShouldApply**的值，其 DWORD 值为1，以避免在登录前使用现有本地配置文件出现问题。

     >[!WARNING]
     >创建 DeleteLocalProfileWhenVHDShouldApply 值时请小心。 如果 FSLogix 配置文件系统确定用户应该具有 FSLogix 配置文件，但本地配置文件已存在，则配置文件容器将永久删除本地配置文件。 然后，将用新的 FSLogix 配置文件登录该用户。

## <a name="assign-users-to-session-host"></a>将用户分配到会话主机

1. 以管理员身份打开**POWERSHELL ISE**并登录到 Windows 虚拟桌面。

2. 运行以下 cmdlet：

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. 当系统提示输入凭据时，请在 Windows 虚拟桌面租户上输入具有租户创建者或 RDS 所有者/RDS 参与者角色的用户的凭据。

4. 运行以下 cmdlet 以将用户分配到远程桌面组：

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>确保用户可以访问 Azure NetApp 文件共享

1. 打开 internet 浏览器，然后访问<https://rdweb.wvd.microsoft.com/arm/webclient>。

2. 使用分配给远程桌面组的用户的凭据登录。

3. 建立用户会话后，请使用管理帐户登录到 Azure 门户。

4. 打开**Azure Netapp 文件**，选择 Azure netapp 文件帐户，然后选择 "**卷**"。 打开 "卷" 菜单后，选择相应的卷。

   ![你之前在 Azure 门户中设置的 NetApp 帐户的屏幕截图，其中选择了 "卷" 按钮。](media/netapp-account.png)

5. 请在 "**概述**" 选项卡上，确认 FSLogix 配置文件容器正在使用空间。

6. 使用远程桌面直接连接到主机池的任何 VM 部分，并打开**文件资源管理器。** 然后导航到**装载路径**（在以下示例中，装载路径为\\ \\anf-SMB-3863.gt1107.onmicrosoft.com\\和）。

   在此文件夹中，应存在一个配置文件 VHD （或 VHDX），如以下示例中所示。

   ![装载路径中文件夹内容的屏幕截图。 内部是名为 "Profile_ssbb" 的单个 VHD 文件。](media/mount-path-folder.png)

## <a name="next-steps"></a>后续步骤

你可以使用 FSLogix 配置文件容器来设置用户配置文件共享。 若要了解如何使用新容器创建用户配置文件共享，请参阅[使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。
