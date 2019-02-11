---
title: 使用 Azure 备份服务器备份 VMware VM
description: 使用 Azure 备份服务器备份 VMware vCenter/ESXi 服务器上运行的 VMware VM。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: ee7ebb151653b611c652c072b8cb4c07754d9b68
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269696"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>使用 Azure 备份服务器备份 VMware VM

本文介绍如何使用 Azure 备份服务器将 VMware ESXi 主机/vCenter 服务器上运行的 VMware VM 备份到 Azure。 

本文介绍以下操作：

- 设置一个安全通道，使 Azure 备份服务器能够通过 HTTPS 来与 VMware 服务器通信。
- 设置一个可供 Azure 备份服务器用来访问 VMware 服务器的 VMware 帐户。
- 将帐户凭据添加到 Azure 备份。
- 将 vCenter 或 ESXi 服务器添加到 Azure 备份服务器。
- 设置一个包含要备份的 VMware VM 的保护组，指定备份设置，并计划备份。

## <a name="before-you-start"></a>开始之前
- 验证运行的 vCenter/ESXi 版本是否支持备份 - 6.5、6.0 和 5.5。 
- 确保已设置 Azure 备份服务器。 如果没有，请在开始之前进行[设置](backup-azure-microsoft-azure-backup.md)。 应运行装有最新更新的 Azure 备份服务器。


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>与 vCenter 服务器建立安全连接

默认情况下，Azure 备份服务器通过 HTTPS 来与 VMware 服务器通信。 若要设置 HTTPS 连接，请下载 VMware 证书颁发机构 (CA) 证书，并将其导入到 Azure 备份服务器。 


### <a name="before-you-start"></a>开始之前

- 如果不想使用 HTTPS，可以[禁用默认设置](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol)。
- 通常，你会使用 vSphere Web 客户端从 Azure 备份服务器计算机上的浏览器连接到 vCenter/ESXi 服务器。 首次执行此操作时，连接并不安全，会显示以下消息。
- 必须了解 Azure 备份服务器处理备份的方式。
    - Azure 备份服务器首先将数据备份到本地磁盘存储。 对于保护的数据，Azure 备份服务器将使用存储池，即，Azure 备份服务器用来存储磁盘恢复点的一组磁盘和卷。 该存储池可以是直接附加存储 (DAS)、光纤通道 SAN，或者 iSCSI 存储设备或 SAN。 必须确保为 VMware VM 数据的本地备份提供足够的存储空间。
    - 然后，Azure 备份服务器会从本地磁盘存储备份到 Azure。
    - 获取测算所需存储空间量的[帮助](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need)。 该信息适用于 DPM，但也适用于 Azure 备份服务器。

### <a name="set-up-the-certificate"></a>设置证书 

按如下所述设置安全通道：

1. 在 Azure 备份服务器上的浏览器中，输入 vSphere Web 客户端 URL。 如果登录页未显示，请验证连接和浏览器代理设置。

    ![vSphere Web 客户端](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. 在 vSphere Web 客户端登录页上，单击“下载受信任的根 CA 证书”。 

    ![下载受信任的根 CA 证书](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. 随后将下载名为 **download** 的文件。 根据所用的浏览器，此时会出现一条消息，询问是要打开还是保存该文件。

    ![下载 CA 证书](./media/backup-azure-backup-server-vmware/download-certs.png)

4. 以 .zip 扩展名将该文件保存在 Azure 备份服务器计算机上。

5. 右键单击“download.zip”并选择“全部解压缩”。 .zip 文件的内容将解压缩到 **certs** 文件夹，其中包含：
    - 根证书文件的扩展名以类似 .0 和 .1 的编号顺序开头。
    - CRL 文件的扩展名以类似 .r0 或 .r1 的序列开头。 CRL 文件与证书关联。

    ![下载的证书](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. 在 **certs** 文件夹中，右键单击根证书文件并选择“重命名”。

    ![重命名根证书 ](./media/backup-azure-backup-server-vmware/rename-cert.png)

6. 将根证书的扩展名更改为 .crt，并确认。 文件图标将更改为表示根证书的图标。

7. 在弹出菜单中右键单击根证书，并选择“安装证书”。 

8. 在“证书导入向导”中，选择“本地计算机”作为证书的目标，然后单击“下一步”。 如果系统询问是否要允许对计算机所做的更改，请确认。

    ![向导中的“欢迎使用”](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)
 

9. 在“证书存储”页面上，选择“将所有证书放入下列存储”，然后单击“浏览”以选择证书存储。

    ![证书存储](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

10. 在“选择证书存储”中，选择“受信任的根证书颁发机构”作为证书的目标文件夹，然后单击“确定”。

    ![证书目标文件夹](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

11. 在“正在完成证书导入向导”中检查文件夹，然后单击“完成”。

    ![验证证书是否位于正确的文件夹中](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    
12. 确认导入证书后，登录到 vCenter 服务器以确认连接安全。


  

### <a name="disable-default-https"></a>禁用默认 HTTPS

如果你在组织中创建了安全边界并且不想要在 VMware 服务器与 Azure 备份服务器计算机之间使用 HTTPS 协议，请按如下所述禁用 HTTPS：
1. 将以下文本复制并粘贴到 .txt 文件中。

      ```
      Windows Registry Editor Version 5.00
      [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
      "IgnoreCertificateValidation"=dword:00000001
      ```

2. 使用文件名 **DisableSecureAuthentication.reg** 将该文件保存在 Azure 备份服务器计算机上。

3. 双击该文件激活该注册表项。


## <a name="create-a-vmware-role"></a>创建 VMware 角色

Azure 备份服务器需要一个有权访问 V-Center 服务器/ESXi 主机的用户帐户。 创建一个具有特定特权的 VMware 角色，然后将某个用户帐户关联到该角色。

1. 登录到 vCenter 服务器（如果不使用 vCenter 服务器，则登录到 ESXi 主机）。
2. 在“导航器”面板中，单击“管理”。

    ![管理 ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. 在“管理” > “角色”中，单击“添加角色”图标（加号）。

    ![添加角色](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    
4. 在“创建角色” > “角色名称”中，输入 *BackupAdminRole*。 角色名称可以是所需的任何内容，但它应该能够帮助识别该角色。

5. 选择下表中汇总的特权，然后单击“确定”。  新角色随即显示在“角色”窗格中的列表内。
    - 单击父标签旁的图标展开父级，并查看子级特权。
    - 若要选择的 VirtualMachine 去权限，需跳转几个级别转到父子层次结构。
    - 不需要选择父特权中的所有子特权。

    ![父子权限层次结构](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>角色权限
**6.5/6.0** | **5.5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField | 
Host.Local.CreateVM | Network.Assign 
Network.Assign | 
Resource.AssignVMToPool | 
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking 
VirtualMachine.Config.HostUSBDevice | 
VirtualMachine.Config.QueryUnownedFiles | 
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement 
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff 
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create 
VirtualMachine.Provisioning.DiskRandomAccess | 
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot 




## <a name="create-a-vmware-account"></a>创建 VMware 帐户

1. 在 vCenter 服务器的“导航器”面板中，单击“用户和组”。 如果不使用 vCenter 服务器，请在相应的 ESXi 主机上创建帐户。

    ![用户和组选项](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    此时将显示“vCenter用户和组”面板。


2. 在“vCenter 用户和组”面板中，选择“用户”选项卡，然后单击“添加用户”图标（加号）。

     ![vCenter 用户和组面板](./media/backup-azure-backup-server-vmware/usersandgroups.png)


3. 在“新建用户”对话框中，添加用户信息并选择“确定”。 在此过程中，用户名是 BackupAdmin。

    ![“新用户”对话框](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)


4. 若要将用户帐户与角色关联，请在“导航器”面板中单击“全局权限”。 在“全局权限”面板中选择“管理”选项卡，然后单击“添加”图标（加号）。

    ![全局权限面板](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)


5. 在“全局权限 Root - 添加权限”中，单击“添加”选择用户或组。

    ![选择用户或组](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. 在“选择用户/组”中，选择“BackupAdmin” > “添加”。 在“用户”中，用户帐户采用“域\用户名”格式。 若要使用其他域，请从“域”列表中选择该域。 单击“确定”，将选定的用户添加到“添加权限”对话框中。

    ![添加 BackupAdmin 用户](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)


7.  在“分配的角色”的下拉列表中，选择“BackupAdminRole” > “确定”。

    ![向角色分配用户](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)


新用户帐户和关联的角色将显示在“全局权限”面板的“管理”选项卡上的列表中。


## <a name="add-the-account-on-azure-backup-server"></a>在 Azure 备份服务器上添加帐户


1. 打开 Azure 备份服务器。 如果在桌面上找不到该图标，请从应用列表中打开“Microsoft Azure 备份”。

    ![Azure 备份服务器图标](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. 在 Azure 备份服务器控制台中，单击“管理” >  “生产服务器” > “管理 VMware”。

    ![Azure 备份服务器控制台](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)


3. 在“管理证书”对话框中，单击“添加”。

    ![Azure 备份服务器的“管理凭据”对话框](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. 在“添加凭据”中，输入新凭据的名称和说明，并指定在 VMware 服务器上定义的用户名和密码。 名称 *Contoso Vcenter credential* 用于标识此过程中的凭据。 如果 VMware 服务器和 Azure 备份服务器不在同一个域中，请在用户名中指定域。

    ![Azure 备份服务器的“添加凭据”对话框](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. 单击“添加”以添加新凭据。

    ![Azure 备份服务器的“管理凭据”对话框](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)


## <a name="add-the-vcenter-server"></a>添加 vCenter 服务器 

将 vCenter 服务器添加到 Azure 备份服务器。


1. 在 Azure 备份服务器控制台中，单击“管理” > “生产服务器” > “添加”。

    ![打开生产服务器添加向导](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)
   

2. 在“生产服务器添加向导” > “选择生产服务器类型”页中，选择“VMware 服务器”，然后单击“下一步”。

     ![生产服务器添加向导](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. 在“选择计算机”>“服务器名称/IP 地址”中，指定 VMware 服务器的 FQDN 或 IP 地址。 如果所有 ESXi 服务器由同一个 vCenter 管理，请指定 vCenter 名称。 否则请添加 ESXi 主机。

    ![指定 VMware 服务器](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. 在“SSL 端口”中，输入用于与 VMware 服务器通信的端口。 443 是默认端口，但如果 VMware 服务器在不同的端口上侦听，则你可以更改端口。

5. 在“指定凭据”中，选择先前创建的凭据。

    ![指定凭据](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. 单击“添加”将 VMware 服务器添加到服务器列表。 然后单击“下一步”。

    ![添加 VMWare 服务器和凭据](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. 在“摘要”页中单击“添加”，将 VMware 服务器添加到 Azure 备份服务器。 新服务器会立即添加，无需在 VMware 服务器上安装代理。

    ![将 VMware 服务器添加到 Azure 备份服务器](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. 在“完成”页上检查设置。

  ![“完成”页面](./media/backup-azure-backup-server-vmware/summary-screen.png)

如果有多个 ESXi 主机不受 vCenter 服务器的管理，或者有多个 vCenter 服务器实例，则需要重新运行向导来添加服务器。 




## <a name="configure-a-protection-group"></a>配置保护组

添加要备份的 VMware VM。 保护组收集多个 VM，并将相同的数据保留和备份设置应用到组中的所有 VM。 


1. 在 Azure 备份服务器控制台中，单击“保护”>“新建”。

    ![打开“新建保护组”向导](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

2. 在“新建保护组”向导的欢迎页中，单击“下一步”。

    ![“新建保护组”向导对话框](./media/backup-azure-backup-server-vmware/protection-wizard.png)

3. 在“选择保护组类型”页面上选择“服务器”，然后单击“下一步”。 将出现“选择组成员”页面。

3. 在“选择组成员”中，选择要备份的 VM（或 VM 文件夹）。 然后单击“下一步”。

    - 选择某个文件夹时，也会选择该文件夹中的 VM 或子文件夹进行备份。 可以取消选中不想要备份的文件夹或 VM。
- 如果 VM 或文件夹已在备份，则无法选择它。 这可以确保不会为 VM 创建重复的恢复点。 .

    ![选择组成员](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)


4. 在“选择数据保护方法”页中，输入保护组的名称和保护设置。 若要备份到 Azure，请将短期保护设置为“磁盘”，并启用联机保护。 然后单击“下一步”。

    ![选择数据保护方法](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. 在“指定短期目标”中，指定要在磁盘中备份数据多长时间。
    - 在“保留期”中，指定保留磁盘恢复点的天数。 
    - 在“同步频率”中，指定创建磁盘恢复点的频率。
        - 如果不想要设置备份间隔，可以选中“紧靠在恢复点之前”，以便计划每个恢复点之前的那一刻运行备份。
        - 短期备份是完整备份而不是增量备份。
        - 单击“修改”以更改执行短期备份的时间/日期。

    ![指定短期目标](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. 在“检查磁盘分配”中，检查为 VM 备份提供的磁盘空间。 对于 VM。

    - 建议的磁盘分配基于指定的保留期、工作负荷类型，以及受保护数据的大小。 做出所需的任何更改，然后单击“下一步”。
    -  **数据大小：** 保护组中数据的大小。
    - **磁盘空间：** 为保护组建议的磁盘空间量。 若要修改此设置，所分配的总空间应比每个数据源预计增长量略大。
    - **共置数据：** 如果启用共置，受保护的多个数据源可以映射到单个副本和恢复点卷。 并非所有工作负荷都支持归置。
    - **自动增长：** 如果启用此设置，当受保护组中的数据超过初始分配时，Azure 备份服务器会尝试将磁盘大小增加 25%。
    - **存储池详细信息：** 显示存储池的状态，包括总磁盘大小和剩余磁盘大小。

    ![查看磁盘分配](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

7. 在“选择副本创建方法”页中指定如何创建初始备份，然后单击“下一步”。
    - 默认设置为“自动通过网络”和“立即”。
    - 若使用默认设置，则建议指定非高峰时间。 选择“稍后”并指定日期和时间。
    - 如果数据量很大或者网络状态欠佳，请考虑使用可移动介质脱机复制数据。

    ![选择副本创建方法](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. 在“一致性检查选项”中，选择如何以及何时自动执行一致性检查。 然后单击“下一步”。
    - 当副本数据变得不一致时，可以运行一致性检查；也可以根据设置的计划运行该检查。
    - 如果不想配置自动一致性检查，可运行手动检查。 为此，请右键单击保护组并选择“执行一致性检查”。

9. 在“指定联机保护数据”页中，选择要备份的 VM 或 VM 文件夹。 可以选择单个成员，或者单击“全选”选择所有成员。 然后单击“下一步”。

    ![指定在线保护数据](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. 在“指定联机备份计划”页中，指定将数据从本地存储备份到 Azure 的频率。

    - 将根据计划生成数据的云恢复点。 然后单击“下一步”。
    - 生成恢复点后，该恢复点将传输到 Azure 中的恢复服务保管库。 
    
    ![指定联机备份计划](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. 在“指定联机保留策略”页中，指明要在 Azure 中将通过每天/每周/每月/每年备份创建的恢复点保留多长时间。 然后单击“下一步”。

    - 在 Azure 中保留数据的时间长短没有限制。
    - 唯一的限制是每个受保护实例的恢复点不可超过 9999 个。 在本示例中，受保护的实例是 VMware 服务器。

    ![指定联机保留策略](./media/backup-azure-backup-server-vmware/retention-policy.png)

   
12. 在“摘要”页中检查设置，然后单击“创建组”。

    ![保护组成员和设置摘要](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>后续步骤

设置备份时若要排查问题，请查看 [Azure 备份服务器的故障排除指南](./backup-azure-mabs-troubleshoot.md)。
