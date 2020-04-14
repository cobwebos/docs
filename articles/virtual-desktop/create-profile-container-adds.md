---
title: 创建 FSLogix 配置文件容器 Azure 文件 活动目录域服务 - Azure
description: 本文介绍如何使用 Azure 文件和 Azure 活动目录域服务创建 FSLogix 配置文件容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dd01b950435fadb96a961b6bb1c6b28ff436907a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265767"
---
# <a name="create-an-fslogix-profile-container-with-azure-files"></a>使用 Azure 文件创建 FSLogix 配置文件容器

本文将介绍如何使用 Azure 文件和 Azure 活动目录域服务 （AD DS） 创建 FSLogix 配置文件容器。

## <a name="prerequisites"></a>先决条件

本文假定您已经设置了 Azure AD DS 实例。 如果还没有，请先按照["创建基本托管域](../active-directory-domain-services/tutorial-create-instance.md)"中的说明操作，然后返回此处。

## <a name="add-azure-ad-ds-admins"></a>添加 Azure AD DS 管理员

要添加其他管理员，请创建新用户并授予他们权限。

要添加管理员：

1. 从边栏中选择**Azure 活动目录**，然后选择**所有用户**，然后选择 **"新建用户**"。

2.  在字段中输入用户详细信息。

3. 在屏幕左侧的 Azure 活动目录窗格中，选择 **"组**"。

4. 选择**AAD DC 管理员**组。

5. 在左侧窗格中，选择 **"成员**"，然后在主窗格中选择 **"添加成员**"。 这将显示 Azure AD 中可用的所有用户的列表。 选择您刚刚创建的用户配置文件的名称。

## <a name="set-up-an-azure-storage-account"></a>设置 Azure 存储帐户

现在是时候通过服务器消息块 （SMB） 启用 Azure AD DS 身份验证了。 

要启用身份验证：

1. 如果尚未设置和部署通用 v2 Azure 存储帐户，请按照[创建 Azure 存储帐户](../storage/common/storage-account-create.md)中的说明进行设置和部署。

2. 设置完帐户后，选择 **"转到资源**"。

3. 从屏幕左侧的窗格中选择 **"配置**"，然后在主窗格中为 Azure 文件启用**Azure 活动目录身份验证**。 完成后，选择“保存”****。

4. 在屏幕左侧的窗格中选择 **"概述**"，然后在主窗格中选择 **"文件**"。

5. 选择 **"文件共享**"，并在屏幕右侧显示的字段中输入 **"名称**和**配额**"。

## <a name="assign-access-permissions-to-an-identity"></a>为标识分配访问权限

其他用户将需要访问权限才能访问您的文件共享。 为此，您需要为每个用户分配具有相应访问权限的角色。

要分配用户访问权限，

1. 从 Azure 门户打开在[设置 Azure 存储帐户](#set-up-an-azure-storage-account)中创建的文件共享。

2. 选择**访问控制 （IAM）。**

3. 选择 **"添加角色分配**"。

4. 在"**添加角色分配"** 选项卡中，从角色列表中选择适当的内置角色。 至少需要为帐户选择**存储文件数据 SMB 共享参与者**才能获得适当的权限。

5. 要**分配对**的访问权限，请选择**Azure 活动目录用户、组或服务主体**。

6. 为目标 Azure 活动目录标识选择名称或电子邮件地址。

7. 选择“保存”。 

## <a name="get-the-storage-account-access-key"></a>获取存储帐户访问密钥

接下来，您需要获取存储帐户的访问密钥。

要获取存储帐户访问密钥，可以：

1. 从 Azure 门户侧边栏中，选择**存储帐户**。

2. 从存储帐户列表中，选择启用 Azure AD DS 的帐户，并在上述步骤中创建自定义角色。

3. 在 **"设置"** 下，选择 **"访问键"** 并从**密钥1**复制密钥。

4. 转到 **"虚拟机"** 选项卡，并找到将成为主机池一部分的任何 VM。

5. 在**虚拟机 （adVM）** 下选择虚拟机 （VM） 的名称，然后选择 **"连接"**

    这将下载 RDP 文件，该文件允许您使用自己的凭据登录到 VM。

    !["连接到虚拟机"窗口的 RDP 选项卡的屏幕截图。](media/rdp-tab.png)

6. 登录到 VM 后，以管理员身份运行命令提示。

7. 运行以下命令：

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - 替换为`<desired-drive-letter>`您选择的驱动器号（例如， `y:`
    - 将 的所有`<storage-account-name>`实例替换为前面指定的存储帐户的名称。
    - 替换为`<share-name>`您之前创建的共享的名称。
    - 替换为`<storage-account-key>`Azure 中的存储帐户密钥。

    例如：  
  
     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. 运行以下命令以授予用户对 Azure 文件共享的完全访问权限。

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - 替换为`<mounted-drive-letter>`您希望用户使用的驱动器的字母。
    - 替换为`<user-email>`将使用此配置文件访问会话主机 VM 的用户的 UPN。

    例如：
     
     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>创建配置文件容器

现在，您的配置文件已准备就绪，让我们创建一个 FSLogix 配置文件容器。

要配置 FSLogix 配置文件容器：

1. 登录到本文开头配置的会话主机 VM，然后[下载并安装 FSLogix 代理](/fslogix/install-ht/)。

2. 解压缩您下载的 FSLogix 代理文件并转到**x64** > **版本**，然后打开**FSLogixAppsSetup.exe**。

3. 安装程序启动后，选择 **"我同意许可证条款和条件"。** 如果适用，请提供新密钥。

4. 选择“安装”  。

5. 打开**驱动器 C**，然后转到**程序文件** > **FSLogix** > **应用程序**，以确保 FSLogix 代理已正确安装。

     >[!NOTE]
     > 如果主机池中有多个 VM，则需要为每个 VM 重复步骤 1 到 5。

6. 以管理员身份运行**注册表编辑器**（RegEdit）。

7. 导航到**计算机** > **HKEY_LOCAL_MACHINE** > **软件** > **FSLogix，** 右键点击**FSLogix，** 选择 **"新建**"，然后选择 **"键**"。

8. 创建名为**配置文件**的新密钥。

9.  右键单击**配置文件**，选择 **"新建**"，然后选择**DWORD（32 位）值。** 命名"**已启用**"值并将**数据**值设置为**1**。

    ![配置文件密钥的屏幕截图。 REG_DWORD文件高亮显示，其数据值设置为 1。](media/dword-value.png)

10. 右键单击**配置文件**，选择 **"新建**"，然后选择**多字符串值**。 命名值**VHDData，** 并将输入 Azure 文件共享`\\fsprofile.file.core.windows.net\share`的 URI 作为数据值。

    ![显示 VHDLocations 文件的配置文件密钥的屏幕截图。 其数据值显示 Azure 文件共享的 URI。](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>将用户分配给会话主机

现在，您需要将用户分配给会话主机。

要分配用户：

1. 以管理员身份运行 Windows PowerShell，然后运行以下 cmdlet 以使用 PowerShell 登录到 Windows 虚拟桌面：

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   当提示您获得凭据时，输入在 Windows 虚拟桌面租户上被授予租户创建者、RDS 所有者或 RDS 参与者角色的同一用户。

2. 运行以下 cmdlet 将用户分配给远程桌面组：

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    与前面的 cmdlet 一样，请确保`<your-wvd-tenant>`用`<wvd-pool>`相关值`<user-principal>`替换 和 。

    例如：

     ```powershell
     $pool1 = "contoso"
     
     $tenant = "contoso"
     
     $appgroup = "Desktop Application Group"
     
     $user1 = "jane.doe@contoso.com"
     
     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>确保您的个人资料有效

现在，您所要做的就是确保您创建的配置文件存在并按预期工作。

要验证您的个人资料：

1. 打开浏览器，转到[Windows 虚拟桌面 Web 客户端](https://rdweb.wvd.microsoft.com/webclient/index.html)。

2. 使用分配给远程桌面组的用户帐户登录。

3. 建立用户会话后，打开 Azure 门户并使用管理帐户登录。

4. 从边栏中，选择 **"存储帐户**"。

5. 选择已配置为会话主机池的文件共享的存储帐户，并使用 Azure AD DS 启用。

6. 选择 **"文件"** 图标，然后展开共享。

    如果一切设置正确，您应该会看到一个名称为"目录"，该**目录**的名称格式如下所示： `<user SID>-<username>`。

## <a name="next-steps"></a>后续步骤

如果您正在寻找创建 FSLogix 配置文件容器的替代方法，请查看以下文章：

- [使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。
- [使用 Azure NetApp 文件为主机池创建 FSLogix 配置文件容器](create-fslogix-profile-container.md)

您可以在[FSLogix 配置文件容器和 Azure 文件中](fslogix-containers-azure-files.md)找到有关与 Azure 文件 FSlogix 容器相关的概念的更多详细信息。
