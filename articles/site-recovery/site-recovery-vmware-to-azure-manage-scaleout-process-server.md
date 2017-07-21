---
title: " 在 Azure Site Recovery 中管理横向扩展进程服务器 | Microsoft Doc"
description: "本文介绍如何在 Azure Site Recovery 中设置和管理横向扩展进程服务器。"
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
ms.openlocfilehash: e5c01de19917235c34c035415df86291b9152bf0
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017

---

# <a name="manage-a-scale-out-process-server"></a>管理横向扩展进程服务器

横向扩展进程服务器充当 Site Recovery 服务和本地基础结构之间的数据传输的协调者。 本文介绍如何设置、配置和管理横向扩展进程服务器。

## <a name="prerequisites"></a>先决条件
以下是建议的设置横向扩展进程服务器所需的硬件、软件和网络配置。

> [!NOTE]
> [容量规划](site-recovery-capacity-planner.md)是一个重要的步骤，用于确保使用符合你的负载要求的配置部署横向扩展进程服务器。 请阅读有关[横向扩展进程服务器的扩展特征](#sizing-requirements-for-a-configuration-server)的更多信息。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>下载横向扩展进程服务器软件
1. 登录 Azure 门户并浏览到恢复服务保管库。
2. 浏览到“Site Recovery 基础结构” > “配置服务器”（在“针对 VMware 和物理计算机”下面）。
3. 选择你的配置服务器以向下钻取到此配置服务器的详细信息页。
4. 单击“+ 进程服务器”按钮。
5. 在“添加进程服务器”页上，从“选择要部署进程服务器的位置”下拉列表中选择“在本地部署横向扩展进程服务器”。

  ![添加服务器页](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. 单击“下载 Microsoft Azure Site Recovery 统一安装”链接以下载最新版本的横向扩展进程服务器的安装程序。

  > [!WARNING]
  横向扩展进程服务器的版本应等于或小于你的环境中运行的配置服务器版本。 确保版本兼容性的一种简单方法是使用最近用来安装/更新配置服务器的相同的安装程序。

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>从 GUI 安装和注册横向扩展进程服务器
如果必须将部署扩展到 200 台以上源计算机，或者每日改动率总计超过 2 TB，则需要额外的进程服务器来处理流量。

请查看[进程服务器的大小建议](#size-recommendations-for-the-process-server)，然后按照这些说明来设置进程服务器。 设置服务器后，可以迁移源计算机来使用它。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>使用命令行安装和注册横向扩展进程服务器

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>示例用法
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>横向扩展进程服务器安装程序命令行参数。
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>创建代理设置配置文件
ProxySettingsFilePath 参数使用某个文件作为输入。 创建使用以下格式的文件并将其作为输入 ProxySettingsFilePath 参数进行传递。
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>修改横向扩展进程服务器的代理设置
1. 登录横向扩展进程服务器。
2. 打开管理员 PowerShell 命令窗口。
3. 运行以下命令
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. 接下来浏览到的目录 **%PROGRAMDATA%\ASR\Agent** 并运行以下命令
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>重新注册横向扩展进程服务器
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* 接下来，打开管理员命令提示符。
* 浏览到目录 **%PROGRAMDATA%\ASR\Agent** 并运行以下命令

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>升级横向扩展进程服务器
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>停用横向扩展进程服务器
1. 请确保：
  - 在 Azure 门户中配置服务器的连接状态显示为**已连接**
  - 进程服务器仍能够与配置服务器通信。
2. 以管理员身份登录进程服务器
3. 打开“控制面板”>“程序”>“卸载程序”
4. 按照以下指定的顺序卸载程序：
  * Microsoft Azure Site Recovery 配置服务器/进程服务器
  * Microsoft Azure Site Recovery 配置服务器依赖项
  * Microsoft Azure 恢复服务代理

进程服务器的删除反映在 Azure 门户中需要 15 分钟。

  > [!NOTE]
  如果进程服务器无法与配置服务器通信（门户中的连接状态为“已断开”），则需要按照以下步骤从配置服务器中清除它。

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>从配置服务器中撤消注册已断开连接的横向扩展进程服务器

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>横向扩展进程服务器的大小调整要求

| **额外的进程服务器** | **缓存磁盘大小** | **数据更改率** | **受保护的计算机** |
| --- | --- | --- | --- |
|4 个 vCPU（2 个插槽 * 2 个核心 @ 2.5 GHz），8 GB 内存 |300 GB |250 GB 或更少 |复制 85 台或更少的计算机。 |
|8 个 vCPU（2 个插槽 * 4 个核心 @ 2.5 GHz），12 GB 内存 |600 GB |250 GB 到 1 TB |复制 85-150 台计算机。 |
|12 个 vCPU（2 个插槽 * 6 个核心 @ 2.5 GHz），24 GB 内存 |1 TB |1 TB 到 2 TB |复制 150-225 台计算机。 |

