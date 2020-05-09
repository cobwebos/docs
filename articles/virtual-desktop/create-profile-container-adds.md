---
title: Active Directory 域服务 azure 文件创建 FSLogix 配置文件容器-Azure
description: 本文介绍如何使用 Azure 文件和 Azure Active Directory 域服务创建 FSLogix 配置文件容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 916d34abfaf8223e3cf29977e13dfddf15a3fbf9
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607276"
---
# <a name="create-an-fslogix-profile-container-with-azure-files"></a>使用 Azure 文件创建 FSLogix 配置文件容器

本文将演示如何使用 Azure 文件和 Azure Active Directory 域服务（AD DS）创建 FSLogix 配置文件容器。

## <a name="prerequisites"></a>先决条件

本文假设已设置 Azure AD DS 实例。 如果还没有，请按照[创建基本托管域](../active-directory-domain-services/tutorial-create-instance.md)中的说明进行操作，然后返回此处。

## <a name="add-azure-ad-ds-admins"></a>添加 Azure AD DS 管理员

若要添加其他管理员，请创建新用户并向其授予权限。

添加管理员：

1. 从边栏中选择 " **Azure Active Directory** "，并选择 "**所有用户**"，然后选择 "**新建用户**"。

2.  在字段中输入用户详细信息。

3. 在屏幕左侧的 Azure Active Directory 窗格中，选择 "**组**"。

4. 选择 " **AAD DC 管理员**" 组。

5. 在左窗格中，选择 "**成员**"，然后选择主窗格中的 "**添加成员**"。 这将显示 Azure AD 中可用的所有用户的列表。 选择刚创建的用户配置文件的名称。

## <a name="set-up-an-azure-storage-account"></a>设置 Azure 存储帐户

现在，可以通过服务器消息块（SMB）启用 Azure AD DS 身份验证。 

启用身份验证：

1. 如果尚未这样做，请按照[创建 Azure 存储帐户](../storage/common/storage-account-create.md)中的说明设置和部署通用 V2 Azure 存储帐户。

2. 完成帐户设置后，选择 "**前往资源**"。

3. 在屏幕左侧的窗格中选择 "**配置**"，然后在主窗格中**为 Azure 文件启用 Azure Active Directory 身份验证**。 完成后，选择“保存”****。

4. 在屏幕左侧的窗格中选择 "**概述**"，然后在主窗格中选择 "**文件**"。

5. 选择 "**文件共享**"，然后在屏幕右侧显示的字段中输入**名称**和**配额**。

## <a name="assign-access-permissions-to-an-identity"></a>为标识分配访问权限

其他用户将需要访问权限才能访问文件共享。 为此，你需要为每个用户分配一个具有适当访问权限的角色。

若要分配用户访问权限：

1. 在 Azure 门户中，打开在[设置 Azure 存储帐户](#set-up-an-azure-storage-account)中创建的文件共享。

2. 选择 "**访问控制（IAM）**"。

3. 选择 "**添加角色分配**"。

4. 在 "**添加角色分配**" 选项卡中，从 "角色" 列表中选择适当的内置角色。 至少需要为帐户选择**存储文件数据 SMB 共享参与者**，才能获取适当的权限。

5. 对于 "分配对的**访问权限**"，请选择**Azure Active Directory 用户、组或服务主体**。

6. 选择目标 Azure Active Directory 标识的名称或电子邮件地址。

7. 选择“保存”  。

## <a name="get-the-storage-account-access-key"></a>获取存储帐户访问密钥

接下来，需要获取存储帐户的访问密钥。

获取存储帐户访问密钥：

1. 在 Azure 门户边栏中，选择 "**存储帐户**"。

2. 从存储帐户列表中，选择已 Azure AD DS 启用的帐户，并在上面的步骤中创建自定义角色。

3. 在 "**设置**" 下，选择 "**访问密钥**"，然后复制**key1**中的密钥。

4. 中转到 "**虚拟机**" 选项卡并找到将成为主机池一部分的任何 VM。

5. 选择 "**虚拟机（adVM）** " 下的虚拟机（VM）的名称，然后选择 "**连接**"

    这会下载 RDP 文件，使你能够使用自己的凭据登录到 VM。

    !["连接到虚拟机" 窗口的 "RDP" 选项卡的屏幕截图。](media/rdp-tab.png)

6. 登录到 VM 后，请以管理员身份运行命令提示符。

7. 运行以下命令：

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - 将`<desired-drive-letter>`替换为所选的驱动器号（例如`y:`）。
    - 将的`<storage-account-name>`所有实例替换为前面指定的存储帐户的名称。
    - 将`<share-name>`替换为前面创建的共享的名称。
    - 将`<storage-account-key>`替换为 Azure 中的存储帐户密钥。

    例如：  
  
     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. 运行以下命令，授予用户对 Azure 文件共享的完全访问权限。

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - 将`<mounted-drive-letter>`替换为你希望用户使用的驱动器号。
    - 将`<user-email>`替换为将使用此配置文件访问会话主机 vm 的用户的 UPN。

    例如：
     
     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>创建配置文件容器

现在，配置文件已准备就绪，接下来创建一个 FSLogix 配置文件容器。

配置 FSLogix 配置文件容器：

1. 登录到本文开头部分配置的会话主机 VM，然后[下载并安装 FSLogix 代理](/fslogix/install-ht/)。

2. 解压缩已下载的 FSLogix 代理文件并中转到**x64** > **版本**，并打开**FSLogixAppsSetup**。

3. 安装程序启动后，选择 "**我同意许可条款和条件"。** 如果适用，请提供新的密钥。

4. 选择“安装”  。

5. 打开**驱动器 C**，然后前往**Program Files** > **FSLogix** > **Apps** ，确保已正确安装了 FSLogix 代理。

     >[!NOTE]
     > 如果主机池中有多个 Vm，则需要对每个 VM 重复步骤1至5。

6. 以管理员身份运行**注册表编辑器**（RegEdit）。

7. 导航到 **"计算机** > **HKEY_LOCAL_MACHINE** > **software** > **FSLogix**"，右键单击 " **FSLogix**"，选择 "**新建**"，然后选择 "**密钥**"。

8. 创建**名为 profile 的新**密钥。

9.  右键单击 "**配置文件**"，选择 "**新建**"，然后选择 " **DWORD （32位）值"。** 为**启用**值命名，并将**数据**值设置为**1**。

    ![配置文件密钥的屏幕截图。 将突出显示 REG_DWORD 文件，并将其数据值设置为1。](media/dword-value.png)

10. 右键单击 "**配置文件**"，选择 "**新建**"，然后选择 "**多字符串值**"。 将值命名为**VHDLocations**并设置输入 Azure 文件共享`\\fsprofile.file.core.windows.net\share`的 URI 作为数据值。

    ![显示 VHDLocations 文件的 "配置文件" 项的屏幕截图。 它的数据值显示 Azure 文件共享的 URI。](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>将用户分配到会话主机

现在需要将用户分配到您的会话主机。

分配用户：

1. 以管理员身份运行 Windows PowerShell，然后运行以下 cmdlet，使用 PowerShell 登录到 Windows 虚拟桌面：

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   当系统提示输入凭据时，请输入授予 Windows 虚拟桌面租户上的 TenantCreator、RDS 所有者或 RDS 参与者角色的相同用户。

2. 运行以下 cmdlet 以将用户分配到远程桌面组：

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    与前面的 cmdlet 一样，请确保将`<your-wvd-tenant>`、 `<wvd-pool>`和`<user-principal>`替换为相关值。

    例如：

     ```powershell
     $pool1 = "contoso"
     
     $tenant = "contoso"
     
     $appgroup = "Desktop Application Group"
     
     $user1 = "jane.doe@contoso.com"
     
     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>请确保配置文件正常工作

现在你只需确保你创建的配置文件存在并按预期工作。

验证配置文件：

1. 打开浏览器并中转到[Windows 虚拟桌面 web 客户端](https://rdweb.wvd.microsoft.com/arm/webclient)。

2. 用分配给远程桌面组的用户帐户登录。

3. 建立用户会话后，打开 Azure 门户并使用管理帐户登录。

4. 从边栏中，选择 "**存储帐户**"。

5. 选择配置为你的会话主机池的文件共享的存储帐户，并 Azure AD DS 启用。

6. 选择 "**文件**" 图标，然后展开你的共享。

    如果所有内容设置正确，应会看到一个**目录**，其名称格式如下： `<user SID>-<username>`。

## <a name="next-steps"></a>后续步骤

如果你正在寻找创建 FSLogix 配置文件容器的替代方法，请查看以下文章：

- [使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。
- [使用 Azure NetApp 文件为主机池创建 FSLogix 配置文件容器](create-fslogix-profile-container.md)

可在[FSlogix 配置文件容器和 Azure 文件](fslogix-containers-azure-files.md)中找到有关与 azure 文件的 FSlogix 容器相关的概念的更多详细信息。
