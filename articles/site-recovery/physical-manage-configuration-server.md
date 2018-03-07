---
title: " 通过 Azure Site Recovery 管理配置服务器，以便进行物理服务器灾难恢复 | Microsoft Docs"
description: "本文介绍了如何通过 Azure Site Recovery 服务管理现有配置服务器，以便进行物理服务器到 Azure 的灾难恢复。"
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2018
ms.author: anoopkv
ms.openlocfilehash: 7fe68f072ef438e21f3e6d3d52aee9e86e537687
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2018
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>为物理服务器灾难恢复管理配置服务器

使用 [Azure Site Recovery](site-recovery-overview.md) 服务进行物理服务器到 Azure 的灾难恢复时，需要设置本地配置服务器。 配置服务器协调本地计算机与 Azure 之间的通信并管理数据复制。 本文概述部署配置服务器后对其进行管理时要执行的常见任务。

## <a name="prerequisites"></a>先决条件

此表汇总了用于部署本地配置服务器计算机的先决条件。

| **组件** | **要求** |
| --- |---|
| CPU 核心数| 8 |
| RAM | 12 GB|
| 磁盘数目 | 3，包括操作系统磁盘、进程服务器缓存磁盘和用于故障回复保留驱动器 |
| 磁盘可用空间（进程服务器缓存） | 600 GB
| 磁盘可用空间（保留磁盘） | 600 GB|
| 操作系统  | Windows Server 2012 R2 <br> Windows Server 2016 |
| 操作系统区域设置 | 英语(美国)|
| VMware vSphere PowerCLI 版本 | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server 角色 | 请勿启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V |
| 组策略| 请勿启用以下组策略： <br> - 阻止访问命令提示符 <br> - 阻止访问注册表编辑工具 <br> - 信任文件附件的逻辑 <br> - 打开脚本执行 <br> [了解详细信息](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | - 无预先存在的默认网站 <br> - 启用[匿名身份验证](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 启用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 设置  <br> - 端口 443 上没有预先存在的网站/应用程序侦听<br>|
| NIC 类型 | VMXNET3（部署为 VMware VM 时） |
| IP 地址类型 | 静态 |
| Internet 访问权限 | 服务器需要以下 URL 的访问权限： <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi（横向扩展进程服务器不需要此 URL） <br> - time.nist.gov <br> - time.windows.com |
| 端口 | 443（控制通道协调）<br>9443（数据传输）|

## <a name="download-the-latest-installation-file"></a>下载最新的安装文件

Site Recovery 门户中提供了配置服务器安装文件的最新版本。 另外，还可以直接从 [Microsoft 下载中心](http://aka.ms/unifiedsetup)下载该代理。

1. 登录 Azure 门户并浏览到恢复服务保管库。
2. 浏览到“Site Recovery 基础结构” > “配置服务器”（在“针对 VMware 和物理计算机”下面）。
3. 单击“+服务器”按钮。
4. 在“添加服务器”页中，单击“下载”按钮下载注册密钥。 在安装配置服务器的过程中，需要使用此密钥将它注册到 Azure Site Recovery 服务。
5. 单击“下载 Microsoft Azure Site Recovery 统一安装程序”链接，下载最新版本的配置服务器。

  ![下载页](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>安装并注册服务器

1. 运行统一安装程序安装文件。
2. 在“开始之前”中，选择“安装配置服务器和进程服务器”。

    ![开始之前](./media/physical-manage-configuration-server/combined-wiz1.png)

3. 在“第三方软件许可证”中单击“我接受”，下载并安装 MySQL。
4. 在“Internet 设置”中，指定配置服务器上运行的提供程序如何通过 Internet 连接到 Azure Site Recovery。 确保已允许所需的 URL。

    - 如果想要使用当前已在计算机上设置的代理进行连接，请选择“使用代理服务器连接到 Azure Site Recovery”。
    - 如果希望提供程序直接进行连接，请选择“在不使用代理服务器的情况下直接连接到 Azure Site Recovery”。
    - 如果现有代理要求身份验证，或者你想要使用自定义代理进行提供程序连接，请选择“使用自定义代理设置进行连接”，并指定地址、端口和凭据。
     ![防火墙](./media/physical-manage-configuration-server/combined-wiz4.png)
6. 在“先决条件检查”设置中运行检查，确保安装可以运行。 如果看到有关**全局时间同步检查**的警告，请检查系统时钟的时间（“日期和时间”设置）是否与时区相同。

    ![先决条件](./media/physical-manage-configuration-server/combined-wiz5.png)
7. 在“MySQL 配置”中，创建用于登录到要安装的 MySQL 服务器实例的凭据。

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. 在“环境详细信息”中，选择是否要复制 VMware VM。 如果要复制，则安装程序会检查 PowerCLI 6.0 是否已安装。
9. 在“安装位置”中，选择要安装二进制文件和存储缓存的位置。 所选驱动器必须至少有 5 GB 的可用磁盘空间，但我们建议选择至少有 600 GB 可用空间的缓存驱动器。

    ![安装位置](./media/physical-manage-configuration-server/combined-wiz8.png)
10. 在“网络选择”中，指定侦听器（网络适配器和 SSL 端口），以便配置服务器在其上发送和接收复制数据。 端口 9443 是用于发送和接收复制流量的默认端口，但可以根据环境的要求修改此端口号。 除了端口 9443 以外，还要打开端口 443，Web 服务器将使用该端口协调复制操作。 请不要使用端口 443 来发送或接收复制流量。

    ![网络选择](./media/physical-manage-configuration-server/combined-wiz9.png)


11. 在“摘要”中复查信息，并单击“安装”。 安装完成后，将生成通行短语。 启用复制时需要用到它，因此请复制并将它保存在安全的位置。


注册完成后，服务器会显示在保管库的“设置” > “服务器”边栏选项卡中。


## <a name="install-from-the-command-line"></a>从命令行安装

如下所示运行安装文件：

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>示例用法
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>parameters

|参数名称| Type | 说明| 值|
|-|-|-|-|
| /ServerMode|必选|指定是要同时安装配置服务器和进程服务器，还是只安装进程服务器|CS<br>PS|
|/InstallLocation|必选|用于安装组件的文件夹| 计算机上的任意文件夹|
|/MySQLCredsFilePath|必选|MySQL 服务器凭据存储到的文件路径|文件应采用以下指定格式|
|/VaultCredsFilePath|必选|保管库凭据文件的路径|有效的文件路径|
|/EnvType|必选|要保护的环境类型 |VMware<br>NonVMware|
|/PSIP|必选|要用于复制数据传输的 NIC 的 IP 地址| 任何有效的 IP 地址|
|/CSIP|必选|配置服务器侦听时所在的 NIC 的 IP 地址| 任何有效的 IP 地址|
|/PassphraseFilePath|必选|通行短语文件位置的完整路径|有效的文件路径|
|/BypassProxy|可选|指定配置服务器不使用代理连接到 Azure|若要从 Venu 获取此值|
|/ProxySettingsFilePath|可选|代理设置（默认代理需要身份验证，或自定义代理）|文件应采用以下指定格式|
|DataTransferSecurePort|可选|PSIP 上用于复制数据的端口号| 有效端口号（默认值为 9433）|
|/SkipSpaceCheck|可选|跳过缓存磁盘的空间检查| |
|/AcceptThirdpartyEULA|必选|该标志表示接受第三方 EULA| |
|/ShowThirdpartyEULA|可选|显示第三方 EULA。 如果作为输入提供，将忽略所有其他参数| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>创建 MYSQLCredsFilePath 的文件输入

MySQLCredsFilePath 参数使用某个文件作为输入。 创建使用以下格式的文件并将其作为输入 MySQLCredsFilePath 参数进行传递。
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>创建 ProxySettingsFilePath 的文件输入
ProxySettingsFilePath 参数使用某个文件作为输入。 创建使用以下格式的文件并将其作为输入 ProxySettingsFilePath 参数进行传递。

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>修改代理设置

可以如下所述修改配置服务器计算机的代理设置：

1. 登录到配置服务器。
2. 使用桌面上的快捷方式启动 cspsconfigtool.exe。
3. 单击“保管库注册”选项卡。
4. 从门户下载新的保管库注册文件，并将其作为输入提供给该工具。

  ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. 提供新代理服务器的详细信息，并单击“注册”按钮。
6. 打开管理员 PowerShell 命令窗口。
7. 运行以下命令：
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  如果向配置服务器附加了更多的进程服务器，则需要在部署中[修复所有横向扩展进程服务器上的代理设置](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server)。

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>将配置服务器重新注册到同一保管库
  1. 登录到配置服务器。
  2. 使用桌面上的快捷方式启动 cspsconfigtool.exe。
  3. 单击“保管库注册”选项卡。
  4. 从门户下载新的注册文件，并将其作为输入提供给该工具。
        ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
  5. 提供代理服务器的详细信息，并单击“注册”按钮。  
  6. 打开管理员 PowerShell 命令窗口。
  7. 运行以下命令

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  如果有多个进程服务器，则需要[重新注册它们](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server)。

## <a name="register-a-configuration-server-with-a-different-vault"></a>将配置服务器注册到不同的保管库

> [!WARNING]
> 以下步骤将配置服务器从当前保管库取消关联，并停止配置服务器下所有受保护虚拟机的复制。

1. 登录到配置服务器
2. 在管理员命令提示符中，运行以下命令：

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. 使用桌面上的快捷方式启动 cspsconfigtool.exe。
4. 单击“保管库注册”选项卡。
5. 从门户下载新的注册文件，并将其作为输入提供给该工具。
6. 提供代理服务器的详细信息，并单击“注册”按钮。  
7. 打开管理员 PowerShell 命令窗口。
8. 运行以下命令
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>升级配置服务器

运行更新汇总来更新配置服务器。 最多可以为 N-4 版本应用更新。 例如：

- 如果运行的是 9.7、9.8、9.9 或 9.10 版，可以直接升级到 9.11 版。
- 如果运行的是 9.6 版或更早版本并且想要升级到 9.11 版，则必须先升级到 9.7 版， 然后再升级到 9.11 版。

[wiki 更新页](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)中提供了用于升级到配置服务器的所有版本的更新汇总的链接。

按如下所示升级服务器：

1. 将更新安装程序文件下载到配置服务器上。
2. 双击以运行安装程序。
3. 安装程序检测计算机上运行的当前版本。
4. 单击“确定”以确认并运行升级。 


## <a name="delete-or-unregister-a-configuration-server"></a>删除或取消注册配置服务器

> [!WARNING]
> 在开始解除配置服务器之前，请务必执行以下操作。
> 1. 针对此配置服务器下的所有虚拟机[禁用保护](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)。
> 2. 从配置服务器中[取消关联](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy)和[删除](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy)所有复制策略。
> 3. [删除](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery)所有与配置服务器关联的 vCenters 服务器/vSphere 主机。


### <a name="delete-the-configuration-server-from-azure-portal"></a>从 Azure 门户中删除配置服务器
1. 在 Azure 门户中，从“保管库”菜单浏览到“Site Recovery 基础结构” > “配置服务器”。
2. 单击想要解除的配置服务器。
3. 在配置服务器的详细信息页中，单击“删除”按钮。
4. 单击“是”确认删除该服务器。

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>卸载配置服务器及其依赖项
  > [!TIP]
  如果打算再次结合 Azure Site Recovery 重新使用该配置服务器，可以直接跳到步骤 4

1. 以管理员身份登录到配置服务器。
2. 打开“控制面板”>“程序”>“卸载程序”
3. 按以下顺序卸载程序：
  * Microsoft Azure 恢复服务代理
  * Microsoft Azure Site Recovery 移动服务/主目标服务器
  * Microsoft Azure Site Recovery 提供程序
  * Microsoft Azure Site Recovery 配置服务器/进程服务器
  * Microsoft Azure Site Recovery 配置服务器依赖项
  * MySQL Server 5.5
4. 在管理员命令提示窗口中运行以下命令。
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>删除或取消注册配置服务器 (PowerShell)

1. [安装](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) Azure PowerShell 模块
2. 使用命令登录到 Azure 帐户
    
    `Login-AzureRmAccount`
3. 选择其下存在保管库的订阅

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  现在设置保管库上下文
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. 选择配置服务器

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. 删除配置服务器

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Remove-AzureRmSiteRecoveryFabric 中的 -Force 选项可用于强制执行删除配置服务器。

## <a name="renew-ssl-certificates"></a>续订 SSL 证书
配置服务器具有一个内置的 Web 服务器，该服务器协调连接到配置服务器的移动服务、进程服务器和主目标服务器的活动。 Web 服务器使用 SSL 证书对客户端进行身份验证。 该证书在三年后到期，并可随时续订。

### <a name="check-expiry"></a>检查有效期

对于 2016 年 5 月之前的配置服务器部署，证书有效期设置为一年。 如果证书即将到期，将出现以下情况：

- 如果离到期日期有两个月或不到两个月，服务将开始在门户中发送通知以及通过电子邮件发送（如果订阅了 Azure Site Recovery 通知）。
- 保管库资源页上将显示通知横幅。 单击横幅可了解更多详细信息。
- 如果显示“立即升级”按钮，则表示环境中有些组件尚未升级到 9.4.xxxx.x 或更高版本。 在续订证书之前升级组件。 无法在旧版本中进行续订。

### <a name="renew-the-certificate"></a>续订证书

1. 在保管库中，打开“Site Recovery 基础结构” > “配置服务器”，并单击所需配置服务器。
2. 到期日期显示在“配置服务器运行状况”下
3. 单击“续订证书”。 




## <a name="common-issues"></a>常见问题
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>后续步骤

查看有关设置[物理服务器](tutorial-physical-to-azure.md)到 Azure 的灾难恢复的教程。

