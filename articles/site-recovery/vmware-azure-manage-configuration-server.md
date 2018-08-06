---
title: 通过 Azure Site Recovery 管理配置服务器，以便进行 VMware 灾难恢复 | Microsoft Docs
description: 本文介绍了如何通过 Azure Site Recovery 管理现有配置服务器，以便执行 VMware 到 Azure 的 灾难恢复。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: df5b2ecce2a5c9d7c263ee0acc3a49b859b93f7f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346114"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>为 VMware VM 管理配置服务器

使用 [Azure Site Recovery](site-recovery-overview.md) 进行 VMware VM 和物理服务器到 Azure 的灾难恢复时，需要设置本地配置服务器。 配置服务器协调本地 VMware 与 Azure 之间的通信并管理数据复制。 本文概述了在部署配置服务器后对其进行管理时要执行的常见任务。



## <a name="modify-vmware-settings"></a>修改 VMware 设置

可以访问配置服务器，如下所示：
    - 登录到部署它的 VM，然后从桌面快捷方式启动 Azure Site Recovery 配置管理器。
    - 或者，可以从 **https://*ConfigurationServerName*/:44315/** 远程访问配置服务器。 使用管理员凭据登录。
   
### <a name="modify-vmware-server-settings"></a>修改 VMware 服务器设置

1. 要将不同 VMware 服务器与配置服务器相关联，请在登录后选择“添加 vCenter Server/vSphere ESXi 服务器”。
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

- 可[将其他适配器添加到 VM](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)，但必须在将配置服务器注册到保管库之前添加它。
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
   ```

      >[!NOTE] 
      >为了从配置服务器**拉取最新的证书**来横向扩展流程服务器，请执行命令 *“<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe>" --registermt*

  8. 最后，通过执行以下命令重启 obengine。
  ```
          net stop obengine
          net start obengine

## Upgrade the configuration server

You run update rollups to update the configuration server. Updates can be applied for up to N-4 versions. For example:

- If you run 9.7, 9.8, 9.9, or 9.10, you can upgrade directly to 9.11.
- If you run 9.6 or earlier and you want to upgrade to 9.11, you must first upgrade to version 9.7. before 9.11.

Links to update rollups for upgrading to all versions of the configuration server are available in the [wiki updates page](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade the server as follows:

1. In the vault, go to **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. If an update is available, a link appears in the **Agent Version** > column.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download the update installer file to the configuration server.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-click to run the installer.
5. The installer detects the current version running on the machine. Click **Yes** to start the upgrade.
6. When the upgrade completes the server configuration validates.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Click **Finish** to close the installer.

## Delete or unregister a configuration server

1. [Disable protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) for all VMs under the configuration server.
2. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) and [delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) all replication policies from the configuration server.
3. [Delete](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) all vCenter servers/vSphere hosts that are associated with the configuration server.
4. In the vault, open **Site Recovery Infrastructure** > **Configuration Servers**.
5. Select the configuration server that you want to remove. Then, on the **Details** page, select **Delete**.

    ![Delete configuration server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### Delete with PowerShell

You can optionally delete the configuration server by using PowerShell.

1. [Install](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) the Azure PowerShell module.
2. Sign in to your Azure account by using this command:
    
    `Connect-AzureRmAccount`
3. Select the vault subscription.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Set the vault context.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault> Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Retrieve the configuration server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Delete the configuration server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> You can use the **-Force** option in Remove-AzureRmSiteRecoveryFabric for forced deletion of the configuration server.
 
## Generate configuration server Passphrase

1. Sign in to your configuration server, and then open a command prompt window as an administrator.
2. To change the directory to the bin folder, execute the command **cd %ProgramData%\ASR\home\svsystems\bin**
3. To generate the passphrase file, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. Your passphrase will be stored in the file located at **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## Renew SSL certificates

The configuration server has an inbuilt web server, which orchestrates activities of the Mobility Service, process servers, and master target servers connected to it. The web server uses an SSL certificate to authenticate clients. The certificate expires after three years and can be renewed at any time.

### Check expiry

For configuration server deployments before May 2016, certificate expiry was set to one year. If you have a certificate that is going to expire, the following occurs:

- When the expiry date is two months or less, the service starts sending notifications in the portal, and by email (if you subscribed to Site Recovery notifications).
- A notification banner appears on the vault resource page. For more information, select the banner.
- If you see an **Upgrade Now** button, it indicates that some components in your environment haven't been upgraded to 9.4.xxxx.x or higher versions. Upgrade the components before you renew the certificate. You can't renew on older versions.

### Renew the certificate

1. In the vault, open **Site Recovery Infrastructure** > **Configuration Server**. Select the required configuration server.
2. The expiry date appears under **Configuration Server health**.
3. Select **Renew Certificates**. 


## Next steps

Review the tutorials for setting up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
