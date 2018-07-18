---
title: 通过 Azure Site Recovery 管理配置服务器，以便进行 VMware 灾难恢复 | Microsoft Docs
description: 本文介绍了如何通过 Azure Site Recovery 管理现有配置服务器，以便执行 VMware 到 Azure 的 灾难恢复。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 753e123c660b1aacea1157157f0e580e15c47536
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287399"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>为 VMware VM 管理配置服务器

使用 [Azure Site Recovery](site-recovery-overview.md) 进行 VMware VM 和物理服务器到 Azure 的灾难恢复时，需要设置本地配置服务器。 配置服务器协调本地 VMware 与 Azure 之间的通信并管理数据复制。 本文概述了在部署配置服务器后对其进行管理时要执行的常见任务。



## <a name="modify-vmware-settings"></a>修改 VMware 设置

可以访问配置服务器，如下所示：
    - 登录到部署它的 VM，然后从桌面快捷方式启动 Azure Site Recovery 配置管理器。
    - 或者，可以从 **https://*ConfigurationServerName*/:44315/** 远程访问配置服务器。 使用管理员凭据登录。
   
### <a name="modify-vmware-server-settings"></a>修改 VMware 服务器设置

1. 若要将不同 VMware 服务器与配置服务器相关联，请在登录后选择“添加 vCenter Server/vSphere ESXi 服务器”。
2. 输入详细信息，然后选择“确定”。


### <a name="modify-credentials-for-automatic-discovery"></a>修改用于自动发现的凭据

1. 若要更新用于连接到 VMware 服务器以便自动发现 VMware VM 的凭据，请在登录后选择“编辑”。
2. 输入新凭据，然后选择“确定”。

    ![修改 VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>修改用于移动服务安装的凭据

修改在为复制启用的 VMware VM 上自动安装移动服务时使用的凭据。

1. 在登录后，选择“管理虚拟机凭据”
2. 输入新凭据，然后选择“确定”。

    ![修改移动服务凭据](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>修改代理设置

修改配置服务器使用的代理设置，以实现对 Azure 的 Internet 访问。 如果除了在配置服务器计算机上运行的默认进程服务器外，还存在其他进程服务器计算机，则同时修改这两台计算机上的设置。

1. 在登录后到配置服务器后，选择“管理连接”。
2. 更新代理值。 然后，选择“保存”以更新设置。

## <a name="add-a-network-adapter"></a>添加网络适配器

开放虚拟化格式 (OVF) 模板部署具有单个网络适配器的配置服务器 VM。

- 可以[将其他适配器添加到 VM](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)，但必须在将配置服务器注册到保管库中之前添加它。
- 在保管库中注册配置服务器之后，若要添加适配器，请在 VM 属性中添加适配器。 然后，需要在保管库中重新注册服务器。


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>在同一保管库中重新注册配置服务器

可根据需要在同一保管库中重新注册配置服务器。 如果除了在配置服务器计算机上运行的默认进程服务器外，还存在其他进程服务器计算机，请注册这两台计算机。


  1. 在保管库中，打开“管理” > “Site Recovery 基础结构” > “配置服务器”。
  2. 在“服务器”中，选择“下载注册密钥”以下载保管库凭据文件。
  3. 登录到配置服务器计算机。
  4. 在 %ProgramData%\ASR\home\svsystems\bin 中，打开 cspsconfigtool.exe。
  5. 在“保管库注册”选项卡上，单击“浏览”并找到你下载的保管库凭据文件。
  6. 按需提供代理服务器详细信息。 然后选择“注册”。
  7. 打开管理员 PowerShell 命令窗口并运行以下命令：

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

## <a name="upgrade-the-configuration-server"></a>升级配置服务器

运行更新汇总来更新配置服务器。 最多可以为 N-4 版本应用更新。 例如：

- 如果运行的是 9.7、9.8、9.9 或 9.10 版，可以直接升级到 9.11 版。
- 如果运行的是 9.6 版或更早版本并且想要升级到 9.11 版，则必须先升级到 9.7 版， 然后再升级到 9.11 版。

[wiki 更新页](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)中提供了用于升级到配置服务器的所有版本的更新汇总的链接。

按如下所示升级服务器：

1. 在保管库中，转到“管理” > “Site Recovery 基础结构” > “配置服务器”。
2. 如果有可用的更新，链接将显示在“代理版本”> 列中。

    ![更新](./media/vmware-azure-manage-configuration-server/update2.png)

1. 将更新安装程序文件下载到配置服务器上。

    ![更新](./media/vmware-azure-manage-configuration-server/update1.png)

4. 双击以运行安装程序。
2. 安装程序检测计算机上运行的当前版本。 单击“是”开始升级。 
3. 升级完成时，验证服务器配置。

    ![更新](./media/vmware-azure-manage-configuration-server/update3.png)

4. 单击“完成”关闭安装程序。


## <a name="delete-or-unregister-a-configuration-server"></a>删除或取消注册配置服务器

1. 对配置服务器下的所有 VM [禁用保护](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)。
2. 从配置服务器中[取消关联](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)和[删除](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)所有复制策略。
3. [删除](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)与配置服务器关联的所有 vCenters 服务器/vSphere 主机。
4. 在保管库中，打开“Site Recovery 基础结构” > “配置服务器”。
5. 选择要删除的配置服务器。 然后，在“详细信息”页上，选择“删除”。

    ![删除配置服务器](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>使用 PowerShell 进行删除

还可以选择使用 PowerShell 删除配置服务器。

1. [安装](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) Azure PowerShell 模块。
2. 使用以下命令登录到你的 Azure 帐户：
    
    `Connect-AzureRmAccount`
3. 选择保管库订阅。

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  设置保管库上下文。
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. 检索配置服务器。

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. 删除配置服务器。

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> 可使用 Remove-AzureRmSiteRecoveryFabric 中的 -Force 选项强制执行删除配置服务器。
 


## <a name="renew-ssl-certificates"></a>续订 SSL 证书

配置服务器具有一个内置的 Web 服务器，该服务器协调连接到配置服务器的移动服务、进程服务器和主目标服务器的活动。 Web 服务器使用 SSL 证书对客户端进行身份验证。 该证书在三年后到期，并可随时续订。

### <a name="check-expiry"></a>检查有效期

对于 2016 年 5 月之前的配置服务器部署，证书有效期设置为一年。 如果证书即将到期，将出现以下情况：

- 如果离到期日期有两个月或不到两个月，服务将开始在门户中发送通知以及通过电子邮件发送（如果订阅了 Site Recovery 通知）。
- 保管库资源页上将显示通知横幅。 若要了解详细信息，请选择横幅。
- 如果看到了“立即升级”按钮，则表示环境中有些组件尚未升级到 9.4.xxxx.x 或更高版本。 请在续订证书之前升级组件。 无法在旧版本中进行续订。

### <a name="renew-the-certificate"></a>续订证书

1. 在保管库中，打开“Site Recovery 基础结构” > “配置服务器”。 选择所需的配置服务器。
2. 到期日期显示在“配置服务器运行状况”下。
3. 选择“续订证书”。 


## <a name="next-steps"></a>后续步骤

查看有关设置 [VMware VM](vmware-azure-tutorial.md) 到 Azure 的灾难恢复的教程。
