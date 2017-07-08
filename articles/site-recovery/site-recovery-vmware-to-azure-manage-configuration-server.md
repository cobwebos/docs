---
title: " 在 Azure Site Recovery 中管理配置服务器 | Microsoft Docs"
description: "本文介绍如何设置和管理配置服务器。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 36da8c7d0f3ace194522e5288f26069cf46d470e
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017

---

# <a name="manage-a-configuration-server"></a>管理配置服务器

配置服务器充当 Site Recovery 服务与本地基础结构之间的协调器。 本文介绍如何设置、配置和管理配置服务器。

## <a name="prerequisites"></a>先决条件
下面是设置配置服务器所需的最低硬件、软件和网络配置。

> [!NOTE]
> [容量规划](site-recovery-capacity-planner.md)是一个重要的步骤，可确保使用符合负载要求的配置部署配置服务器。 阅读有关[配置服务器大小要求](#sizing-requirements-for-a-configuration-server)的详细信息。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-configuration-server-software"></a>下载配置服务器软件
1. 登录 Azure 门户并浏览到恢复服务保管库。
2. 浏览到“Site Recovery 基础结构” > “配置服务器”（在“适用于 VMware 和物理计算机”下面）。

  ![添加服务器页](./media/site-recovery-vmware-to-azure-manage-configuration-server/AddServers.png)
3. 单击“+服务器”按钮。
4. 在“添加服务器”页中，单击“下载”按钮下载注册密钥。 在安装配置服务器的过程中，需要使用此密钥将它注册到 Azure Site Recovery 服务。
5. 单击“下载 Microsoft Azure Site Recovery 统一安装程序”链接，下载最新版本的配置服务器。

  ![下载页](./media/site-recovery-vmware-to-azure-manage-configuration-server/downloadcs.png)

  > [!TIP]
  可以直接从 [Microsoft 下载中心下载页](http://aka.ms/unifiedsetup)下载最新版本的配置服务器

## <a name="installing-and-registering-a-configuration-server-from-gui"></a>通过 GUI 安装并注册配置服务器
[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

## <a name="installing-and-registering-a-configuration-server-using-command-line"></a>使用命令行安装并注册配置服务器

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>示例用法
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="configuration-server-installer-command-line-arguments"></a>配置服务器安装程序命令行参数。
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-mysql-credentials-file"></a>创建 MySql 凭据文件
MySQLCredsFilePath 参数使用某个文件作为输入。 创建使用以下格式的文件并将其作为输入 MySQLCredsFilePath 参数进行传递。
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-a-proxy-settings-configuration-file"></a>创建代理设置配置文件
ProxySettingsFilePath 参数使用某个文件作为输入。 创建使用以下格式的文件并将其作为输入 ProxySettingsFilePath 参数进行传递。

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-configuration-server"></a>修改配置服务器的代理设置
1. 登录到配置服务器。
2. 使用桌面上的快捷方式启动 cspsconfigtool.exe。
3. 单击“保管库注册”选项卡。
4. 从门户下载新的保管库注册文件，并将其作为输入提供给该工具。

  ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
5. 提供新代理服务器的详细信息，然后单击“注册”按钮。
6. 打开管理员 PowerShell 命令窗口。
7. 运行以下命令
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  如果已将横向扩展进程服务器附加到此配置服务器，需在部署中[修复所有横向扩展进程服务器上的代理设置](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server)。

## <a name="re-register-a-configuration-server-with-the-same-recovery-services-vault"></a>将配置服务器重新注册到同一个恢复服务保管库
  1. 登录到配置服务器。
  2. 使用桌面上的快捷方式启动 cspsconfigtool.exe。
  3. 单击“保管库注册”选项卡。
  4. 从门户下载新的注册文件，并将其作为输入提供给该工具。
        ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
  5. 提供代理服务器的详细信息，然后单击“注册”按钮。  
  6. 打开管理员 PowerShell 命令窗口。
  7. 运行以下命令

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  如果已将横向扩展进程服务器附加到此配置服务器，需在部署中[重新注册所有横向扩展进程服务器](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server)。

## <a name="registering-a-configuration-server-with-a-different-recovery-services-vault"></a>将配置服务器注册到不同的恢复服务保管库。
1. 登录到配置服务器。
2. 在管理员命令提示符中，运行命令

```
reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
net stop dra
```
3. 使用桌面上的快捷方式启动 cspsconfigtool.exe。
4. 单击“保管库注册”选项卡。
5. 从门户下载新的注册文件，并将其作为输入提供给该工具。

    ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
6. 提供代理服务器的详细信息，然后单击“注册”按钮。  
7. 打开管理员 PowerShell 命令窗口。
8. 运行以下命令
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="decommissioning-a-configuration-server"></a>解除配置服务器
在开始解除配置服务器之前，请务必执行以下操作。
1. 禁用针对此配置服务器下的所有虚拟机的保护。
2. 从配置服务器中取消关联所有复制策略。
3. 删除所有与配置服务器关联的 vCenters 服务器/vSphere 主机。

### <a name="delete-the-configuration-server-from-azure-portal"></a>从 Azure 门户中删除配置服务器
1. 在 Azure 门户中，从“保管库”菜单浏览到“Site Recovery 基础结构” > “配置服务器”。
2. 单击想要解除的配置服务器。
3. 在配置服务器的详细信息页中，单击“删除”按钮。

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.PNG)
4. 单击“是”确认删除该服务器。

  >[!WARNING]
  如果有任何虚拟机、复制策略或 vCenter 服务器/vSphere 主机与此配置服务器关联，则无法删除此服务器。 请先删除这些实体，然后再尝试删除保管库。

### <a name="uninstall-the-configuration-server-software-and-its-dependencies"></a>卸载配置服务器软件及其依赖项
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

## <a name="renew-configuration-server-secure-socket-layerssl-certificates"></a>续订配置服务器安全套接字层 (SSL) 证书
配置服务器具有一个内置的 Web 服务器，该服务器协调连接到配置服务器的移动服务、进程服务器和主目标服务器的活动。 配置服务器的 Web 服务器使用 SSL 证书对其客户端进行身份验证。 此证书在三年后过期，随时可使用以下方法续订：

> [!WARNING]
只能在 9.4.XXXX.X 或更高版本上执行证书过期。 在启动“续订证书”工作流之前，请升级所有 Azure Site Recovery 组件（配置服务器、进程服务器、主目标服务器、移动服务）。

1. 在 Azure 门户中，浏览到“保管库”>“Site Recovery 基础结构”>“配置服务器”。
2. 单击需要为其续订 SSL 证书的配置服务器。
3. 在“配置服务器运行状况”下面，可以看到 SSL 证书的过期日期。
4. 单击“续订证书”操作续订证书，如下图所示：

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/renew-cert-page.png)

### <a name="secure-socket-layer-certificate-expiry-warning"></a>安全套接字层证书过期警告

> [!NOTE]
在 2016 年 5 月之前为所有安装生成的 SSL 证书的有效期设置为一年。 Azure 门户中应该已经开始显示证书过期通知。

1. 如果配置服务器的 SSL 证书将在未来两个月过期，服务将开始通过 Azure 门户和电子邮件（需要订阅 Azure Site Recovery 通知）通知用户。 随后，保管库的资源页上会开始出现通知横幅。

  ![certificate-notification](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-renew-notification.png)
2. 单击该横幅可获取有关证书过期的其他详细信息。

  ![certificate-details](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-expiry-details.png)

  >[!TIP]
  如果未出现“立即续订”按钮，则会出现“立即升级”按钮。 这表示环境中有些组件尚未升级到 9.4.xxxx.x 或更高版本。

## <a name="sizing-requirements-for-a-configuration-server"></a>配置服务器大小要求

| **CPU** | **内存** | **缓存磁盘大小** | **数据更改率** | **受保护的计算机** |
| --- | --- | --- | --- | --- |
| 8 个 vCPU（2 个插槽 * 4 个核心 @ 2.5 GHz） |16 GB |300 GB |500 GB 或更少 |复制少于 100 台计算机。 |
| 12 个 vCPU（2 个插槽 * 6 个核心 @ 2.5 GHz） |18 GB |600 GB |500 GB 到 1 TB |复制 100-150 台计算机。 |
| 16 个 vCPU（2 个插槽 * 8 个核心 @ 2.5 GHz） |32 GB |1 TB |1 TB 到 2 TB |复制 150-200 台计算机。 |

  >[!TIP]
  如果每日数据更改量超过 2 TB 或者你要复制超过 200 个虚拟机，我们建议部署额外的进程服务器，对复制流量进行负载均衡。 详细了解如何部署横向扩展进程服务器。


## <a name="common-issues"></a>常见问题
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

