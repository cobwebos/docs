---
title: 使用 Azure 备份服务器备份 VMware VM
description: 本文介绍如何使用 Azure 备份服务器备份 VMware vCenter/ESXi 服务器上运行的 VMware VM。
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: db5e5c4bdac64e2faf5babb107ecec61a02d6468
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90069826"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>使用 Azure 备份服务器备份 VMware VM

本文介绍如何使用 Azure 备份服务器 (MABS) 将运行在 VMware ESXi 主机/vCenter Server 上的 VMware Vm 备份到 Azure。

本文介绍如何执行以下操作：

- 设置一个安全通道，使 Azure 备份服务器能够通过 HTTPS 来与 VMware 服务器通信。
- 设置一个可供 Azure 备份服务器用来访问 VMware 服务器的 VMware 帐户。
- 将帐户凭据添加到 Azure 备份。
- 将 vCenter 或 ESXi 服务器添加到 Azure 备份服务器。
- 设置一个包含要备份的 VMware VM 的保护组，指定备份设置，并计划备份。

## <a name="supported-vmware-features"></a>支持的 VMware 功能

在备份 VMware 虚拟机时，MABS 提供以下功能：

- 无代理备份： MABS 不需要在 vCenter 或 ESXi 服务器上安装代理来备份虚拟机。 相反，只需提供 IP 地址或完全限定的域名 (FQDN) ，以及用于通过 MABS 对 VMware 服务器进行身份验证的登录凭据。
- 云集成备份： MABS 保护工作负荷到磁盘和云。 MABS 的备份和恢复工作流可帮助管理长期保留和非现场备份。
- 检测并保护 vCenter 管理的 Vm： MABS 检测并保护部署在 VMware 服务器上 (vCenter 或 ESXi server) 的 Vm。 当部署规模增大时，使用 vCenter 来管理 VMware 环境。 MABS 还检测由 vCenter 管理的 Vm，使你能够保护大型部署。
- 文件夹级自动保护：vCenter 允许组织 VM 文件夹中的 VM。 MABS 检测这些文件夹，并允许在文件夹级别保护 Vm，并包括所有子文件夹。 保护文件夹时，MABS 不仅保护该文件夹中的 Vm，而且还保护稍后添加的 Vm。 MABS 每日检测新的 Vm 并自动对其进行保护。 在将 Vm 组织到递归文件夹中时，MABS 会自动检测并保护递归文件夹中部署的新 Vm。
- MABS 保护存储在本地磁盘、网络文件系统 (NFS) 或群集存储中的 Vm。
- MABS 保护迁移用于负载平衡的 Vm：当迁移 Vm 以实现负载平衡时，MABS 会自动检测并继续 VM 保护。
- MABS 可以从 Windows VM 恢复文件/文件夹，而无需恢复整个 VM，这有助于更快地恢复所需的文件。

## <a name="prerequisites-and-limitations"></a>先决条件和限制

开始备份 VMware 虚拟机之前，请查看以下限制和先决条件的列表。

- 如果使用 MABS 来保护 (在 Windows) 上运行的 vCenter 服务器作为使用服务器的 FQDN 的 Windows Server，则不能使用服务器的 FQDN 将 vCenter 服务器作为 VMware 服务器来保护。
  - 可以使用 vCenter Server 的静态 IP 地址作为一种解决方法。
  - 如果要使用 FQDN，应将保护停止为 Windows Server，删除保护代理，然后使用 FQDN 添加为 VMware 服务器。
- 如果你使用 vCenter 来管理你的环境中的 ESXi 服务器，请将 vCenter (而不是 ESXi) 添加到 MABS 保护组。
- 在第一次 MABS 备份之前，无法备份用户快照。 MABS 完成首次备份后，可以备份用户快照。
- MABS 无法保护具有传递磁盘的 VMware Vm 和物理原始设备映射 (pRDM) 。
- MABS 无法检测或保护 VMware vApps。
- MABS 无法通过现有快照保护 VMware Vm。

## <a name="before-you-start"></a>开始之前

- 验证运行的是否是支持备份的 vCenter/ESXi 版本。 请参阅[此处](./backup-mabs-protection-matrix.md)的支持矩阵。
- 确保已设置 Azure 备份服务器。 如果没有，请在开始之前进行[设置](backup-azure-microsoft-azure-backup.md)。 应运行装有最新更新的 Azure 备份服务器。
- 确保以下网络端口处于打开状态：
  - MABS 与 vCenter 之间的 TCP 443
  - MABS 与 ESXi 主机之间的 TCP 443 和 TCP 902

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>与 vCenter 服务器建立安全连接

默认情况下，Azure 备份服务器通过 HTTPS 来与 VMware 服务器通信。 若要设置 HTTPS 连接，请下载 VMware 证书颁发机构 (CA) 证书，并将其导入到 Azure 备份服务器。

### <a name="before-you-begin"></a>准备阶段

- 如果不想使用 HTTPS，可以[对所有 VMware 服务器禁用 HTTPS 证书验证](backup-azure-backup-server-vmware.md#disable-https-certificate-validation)。
- 通常，你会使用 vSphere Web 客户端从 Azure 备份服务器计算机上的浏览器连接到 vCenter/ESXi 服务器。 首次执行此操作时，连接并不安全，会显示以下消息。
- 必须了解 Azure 备份服务器处理备份的方式。
  - Azure 备份服务器首先将数据备份到本地磁盘存储。 对于保护的数据，Azure 备份服务器将使用存储池，即，Azure 备份服务器用来存储磁盘恢复点的一组磁盘和卷。 该存储池可以是直接附加存储 (DAS)、光纤通道 SAN，或者 iSCSI 存储设备或 SAN。 必须确保为 VMware VM 数据的本地备份提供足够的存储空间。
  - 然后，Azure 备份服务器会从本地磁盘存储备份到 Azure。
  - 获取测算所需存储空间量的[帮助](/system-center/dpm/create-dpm-protection-groups#figure-out-how-much-storage-space-you-need)。 该信息适用于 DPM，但也适用于 Azure 备份服务器。

### <a name="set-up-the-certificate"></a>设置证书

按如下所述设置安全通道：

1. 在 Azure 备份服务器上的浏览器中，输入 vSphere Web 客户端 URL。 如果登录页未显示，请验证连接和浏览器代理设置。

    ![vSphere Web 客户端](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. 在 vSphere Web 客户端登录页上，选择“下载受信任的根 CA 证书”。

    ![下载受信任的根 CA 证书](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. 随后将下载名为 **download** 的文件。 根据所用的浏览器，此时会出现一条消息，询问是打开还是保存该文件。

    ![下载 CA 证书](./media/backup-azure-backup-server-vmware/download-certs.png)

4. 以 .zip 扩展名将该文件保存在 Azure 备份服务器计算机上。

5. 右键单击“download.zip”并选择“全部解压缩”。  .zip 文件的内容将解压缩到 **certs** 文件夹，其中包含：
   - 根证书文件的扩展名以类似 .0 和 .1 的编号顺序开头。
   - CRL 文件的扩展名以类似 .r0 或 .r1 的序列开头。 CRL 文件与证书关联。

    ![下载的证书](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. 在 **certs** 文件夹中，右键单击根证书文件并选择“重命名”。

    ![重命名根证书](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. 将根证书的扩展名更改为 .crt，并确认。 文件图标将更改为表示根证书的图标。

8. 右键单击根证书，然后在弹出菜单中选择“安装证书”。

9. 在“证书导入向导”中，选择“本地计算机”作为证书的目标，然后选择“下一步”  。 如果系统询问是否要允许对计算机所做的更改，请确认。

    ![向导中的“欢迎使用”](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. 在“证书存储”页面上，选择“将所有证书放入下列存储”，然后选择“浏览”以选择证书存储  。

    ![证书存储](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. 在“选择证书存储”中，选择“受信任的根证书颁发机构”作为证书的目标文件夹，然后选择“确定”  。

    ![证书目标文件夹](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. 在“正在完成证书导入向导”中检查文件夹，然后选择“完成” 。

    ![验证证书是否位于正确的文件夹中](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. 确认导入证书后，登录到 vCenter 服务器以确认连接安全。

### <a name="disable-https-certificate-validation"></a>禁用 HTTPS 证书验证

如果你在组织中创建了安全边界并且不想要在 VMware 服务器与 Azure 备份服务器计算机之间使用 HTTPS 协议，请按如下所述禁用 HTTPS：

1. 将以下文本复制并粘贴到 .txt 文件中。

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. 使用文件名 **DisableSecureAuthentication.reg** 将该文件保存在 Azure 备份服务器计算机上。

3. 双击文件激活注册表项。

## <a name="create-a-vmware-role"></a>创建 VMware 角色

Azure 备份服务器需要一个有权访问 V-Center 服务器/ESXi 主机的用户帐户。 创建一个具有特定特权的 VMware 角色，然后将某个用户帐户关联到该角色。

1. 登录到 vCenter 服务器（如果不使用 vCenter 服务器，则登录到 ESXi 主机）。
2. 在“导航器”面板中，选择“管理” 。

    ![管理](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. 在“管理” > “角色”中，选择“添加角色”图标（加号） 。

    ![添加角色](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. 在“创建角色” > “角色名称”中，输入 *BackupAdminRole*。  角色名称可以是所需的任何名称，但应有助于识别该角色。

5. 选择下表中汇总的特权，然后选择“确定”。  新角色随即显示在“角色”窗格中的列表内。
   - 选择父标签旁的图标展开父级，并查看子级特权。
   - 若要选择 VirtualMachine 权限，需跳转几个级别转到父子层次结构。
   - 不需要选择父特权中的所有子特权。

    ![父子权限层次结构](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>角色权限

下表说明了需要向你创建的用户帐户分配的特权：

| 适用于 vCenter 6.5 用户帐户的特权                          | 适用于 vCenter 6.7 用户帐户的特权                            |
|----------------------------------------------------------------------------|----------------------------------------------------------------------------|
| Datastore cluster.Configure a datastore cluster                           | Datastore cluster.Configure a datastore cluster                           |
| Datastore.AllocateSpace                                                    | Datastore.AllocateSpace                                                    |
| Datastore.Browse datastore                                                 | Datastore.Browse datastore                                                 |
| Datastore.Low-level file operations                                        | Datastore.Low-level file operations                                        |
| Global.Disable methods                                                     | Global.Disable methods                                                     |
| Global.Enable methods                                                      | Global.Enable methods                                                      |
| Global.Licenses                                                            | Global.Licenses                                                            |
| Global.Log event                                                           | Global.Log event                                                           |
| Global.Manage custom attributes                                            | Global.Manage custom attributes                                            |
| Global.Set custom attribute                                                | Global.Set custom attribute                                                |
| Host.Local operations.Create virtual machine                               | Host.Local operations.Create virtual machine                               |
| Network.Assign network                                                     | Network.Assign network                                                     |
| Resource. Assign virtual machine to resource pool                          | Resource. Assign virtual machine to resource pool                          |
| vApp.Add virtual machine                                                   | vApp.Add virtual machine                                                   |
| vApp.Assign resource pool                                                  | vApp.Assign resource pool                                                  |
| vApp.Unregister                                                            | vApp.Unregister                                                            |
| VirtualMachine.Configuration. Add Or Remove Device                         | VirtualMachine.Configuration. Add Or Remove Device                         |
| Virtual machine.Configuration.Disk lease                                   | Virtual machine.Configuration.Acquire disk lease                           |
| Virtual machine.Configuration.Add new disk                                 | Virtual machine.Configuration.Add new disk                                 |
| Virtual machine.Configuration.Advanced                                     | Virtual machine.Configuration.Advanced configuration                       |
| Virtual machine.Configuration.Disk change tracking                         | Virtual machine.Configuration.Toggle disk change tracking                  |
| Virtual machine.Configuration.Host USB device                              | Virtual machine.Configuration.Configure Host USB device                    |
| Virtual machine.Configuration.Extend virtual disk                          | Virtual machine.Configuration.Extend virtual disk                          |
| Virtual machine.Configuration.Query unowned files                          | Virtual machine.Configuration.Query unowned files                          |
| Virtual machine.Configuration.Swapfile placement                           | Virtual machine.Configuration.Change Swapfile placement                    |
| Virtual machine.Guest Operations.Guest Operation Program Execution         | Virtual machine.Guest Operations.Guest Operation Program Execution         |
| Virtual machine.Guest Operations.Guest Operation Modifications             | Virtual machine.Guest Operations.Guest Operation Modifications             |
| Virtual machine.Guest Operations.Guest Operation Queries                   | Virtual machine.Guest Operations.Guest Operation Queries                   |
| Virtual machine .Interaction .Device connection                            | Virtual machine .Interaction .Device connection                            |
| Virtual machine .Interaction .Guest operating system management by VIX API | Virtual machine .Interaction .Guest operating system management by VIX API |
| Virtual machine .Interaction .Power Off                                    | Virtual machine .Interaction .Power Off                                    |
| Virtual machine .Inventory.Create new                                      | Virtual machine .Inventory.Create new                                      |
| Virtual machine .Inventory.Remove                                          | Virtual machine .Inventory.Remove                                          |
| Virtual machine .Inventory.Register                                        | Virtual machine .Inventory.Register                                        |
| Virtual machine .Provisioning.Allow disk access                            | Virtual machine .Provisioning.Allow disk access                            |
| Virtual machine .Provisioning.Allow file access                            | Virtual machine .Provisioning.Allow file access                            |
| Virtual machine .Provisioning.Allow read-only disk access                  | Virtual machine .Provisioning.Allow read-only disk access                  |
| Virtual machine .Provisioning.Allow virtual machine download               | Virtual machine .Provisioning.Allow virtual machine download               |
| Virtual machine .Snapshot management. Create snapshot                      | Virtual machine .Snapshot management. Create snapshot                      |
| Virtual machine .Snapshot management.Remove Snapshot                       | Virtual machine .Snapshot management.Remove Snapshot                       |
| Virtual machine .Snapshot management.Revert to snapshot                    | Virtual machine .Snapshot management.Revert to snapshot                    |

> [!NOTE]
> 下表列出了针对 vCenter 6.0 和 vCenter 5.5 用户帐户的特权。

| 适用于 vCenter 6.0 用户帐户的特权 | 适用于 vCenter 5.5 用户帐户的特权 |
| --- | --- |
| Datastore.AllocateSpace | Network.Assign |
| Global.Manage custom attributes | Datastore.AllocateSpace |
| Global.Set custom attribute | VirtualMachine.Config.ChangeTracking |
| Host.Local operations.Create virtual machine | VirtualMachine.State.RemoveSnapshot |
| Network. Assign network | VirtualMachine.State.CreateSnapshot |
| Resource. Assign virtual machine to resource pool | VirtualMachine.Provisioning.DiskRandomRead |
| Virtual machine.Configuration.Add new disk | VirtualMachine.Interact.PowerOff |
| Virtual machine.Configuration.Advanced | VirtualMachine.Inventory.Create |
| Virtual machine.Configuration.Disk change tracking | VirtualMachine.Config.AddNewDisk |
| Virtual machine.Configuration.Host USB device | VirtualMachine.Config.HostUSBDevice |
| Virtual machine.Configuration.Query unowned files | VirtualMachine.Config.AdvancedConfig |
| Virtual machine.Configuration.Swapfile placement | VirtualMachine.Config.SwapPlacement |
| Virtual machine.Interaction.Power Off | Global.ManageCustomFields |
| Virtual machine.Inventory. Create new |   |
| Virtual machine.Provisioning.Allow disk access |   |
| Virtual machine.Provisioning. Allow read-only disk access |   |
| Virtual machine.Snapshot management.Create snapshot |   |
| Virtual machine.Snapshot management.Remove Snapshot |   |

## <a name="create-a-vmware-account"></a>创建 VMware 帐户

1. 在 vCenter 服务器的“导航器”面板中，选择“用户和组” 。 如果不使用 vCenter 服务器，请在相应的 ESXi 主机上创建帐户。

    ![“用户和组”选项](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    此时会显示“vCenter 用户和组”面板。

2. 在“vCenter 用户和组”面板中，选择“用户”选项卡，然后选择“添加用户”图标（加号） 。

    ![“vCenter 用户和组”面板](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. 在“新建用户”对话框中，添加用户信息并选择“确定”。  在此过程中，用户名是 BackupAdmin。

    ![“新建用户”对话框](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. 若要将用户帐户与角色关联，请在“导航器”面板中选择“全局权限” 。 在“全局权限”面板中选择“管理”选项卡，然后选择“添加”图标（加号） 。

    ![“全局权限”面板](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. 在“全局权限 Root - 添加权限”中，选择“添加”选择用户或组 。

    ![选择用户或组](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. 在“选择用户/组”中，选择“BackupAdmin” > “添加”。   在“用户”中，用户帐户采用“域\用户名”格式。 若要使用其他域，请从“域”列表中选择该域。 选择“确定”，将选定的用户添加到“添加权限”对话框中 。

    ![添加 BackupAdmin 用户](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. 在“分配的角色”的下拉列表中，选择“BackupAdminRole” > “确定”。  

    ![向角色分配用户](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

新用户帐户和关联的角色显示在“全局权限”面板的“管理”选项卡的列表中。 

## <a name="add-the-account-on-azure-backup-server"></a>在 Azure 备份服务器上添加帐户

1. 打开 Azure 备份服务器。 如果在桌面上找不到该图标，请从应用列表中打开“Microsoft Azure 备份”。

    ![Azure 备份服务器图标](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. 在 Azure 备份服务器控制台中，选择“管理” >  “生产服务器” > “管理 VMware”  。

    ![Azure 备份服务器控制台](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. 在“管理证书”对话框中，选择“添加” 。

    ![“管理凭据”对话框](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. 在“添加凭据”中，输入新凭据的名称和说明，并指定在 VMware 服务器上定义的用户名和密码。 名称 *Contoso Vcenter credential* 用于标识此过程中的凭据。 如果 VMware 服务器和 Azure 备份服务器不在同一个域中，请在用户名中指定域。

    ![Azure 备份服务器的“添加凭据”对话框](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. 选择“添加”以添加新凭据。

    ![添加新凭据](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>添加 vCenter 服务器

将 vCenter 服务器添加到 Azure 备份服务器。

1. 在 Azure 备份服务器控制台中，选择“管理” > “生产服务器” > “添加”  。

    ![打开生产服务器添加向导](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. 在“生产服务器添加向导” > “选择生产服务器类型”页中，选择“VMware 服务器”，然后选择“下一步”   。

    ![生产服务器添加向导](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. 在“选择计算机”>“服务器名称/IP 地址”中，指定 VMware 服务器的 FQDN 或 IP 地址。  如果所有 ESXi 服务器由同一个 vCenter 管理，请指定 vCenter 名称。 否则请添加 ESXi 主机。

    ![指定 VMware 服务器](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. 在“SSL 端口”中，输入用于与 VMware 服务器通信的端口。 443 是默认端口，但如果 VMware 服务器在不同的端口上侦听，则你可以更改端口。

5. 在“指定凭据”中，选择先前创建的凭据。

    ![指定凭据](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. 选择“添加”将 VMware 服务器添加到服务器列表。 然后，选择“下一步”。

    ![添加 VMWare 服务器和凭据](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. 在“摘要”页中选择“添加”，将 VMware 服务器添加到 Azure 备份服务器 。 新服务器会立即添加，无需在 VMware 服务器上安装代理。

    ![将 VMware 服务器添加到 Azure 备份服务器](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. 在“完成”页上检查设置。

   ![“完成”页](./media/backup-azure-backup-server-vmware/summary-screen.png)

如果有多个 ESXi 主机不受 vCenter 服务器的管理，或者有多个 vCenter 服务器实例，则需要重新运行向导来添加服务器。

## <a name="configure-a-protection-group"></a>配置保护组

添加要备份的 VMware VM。 保护组收集多个 VM，并将相同的数据保留和备份设置应用到组中的所有 VM。

1. 在 Azure 备份服务器控制台中，选择“保护”>“新建” 。

    ![打开“创建新保护组”向导](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. 在“新建保护组”向导的欢迎页中，选择“下一步” 。

    ![“创建新保护组”向导对话框](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. 在“选择保护组类型”页上，选择“服务器”，然后选择“下一步”  。 此时会显示“选择组成员”页。

1. 在“选择组成员”中，选择要备份的 VM（或 VM 文件夹）。 然后，选择“下一步”。

    - 选择某个文件夹时，也会选择该文件夹中的 VM 或子文件夹进行备份。 可以取消选中不想要备份的文件夹或 VM。
1. 如果 VM 或文件夹已在备份，则无法选择它。 这可以确保不会为 VM 创建重复的恢复点。

    ![选择组成员](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. 在“选择数据保护方法”页中，输入保护组的名称和保护设置。 若要备份到 Azure，请将短期保护设置为“磁盘”，并启用联机保护。 然后，选择“下一步”。

    ![选择数据保护方法](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. 在“指定短期目标”中，指定要在磁盘中备份数据多长时间。
   - 在“保留期”中，指定保留磁盘恢复点的天数。
   - 在“同步频率”中，指定创建磁盘恢复点的频率。
       - 如果不想要设置备份间隔，可以选中“紧靠在恢复点之前”，以便计划每个恢复点之前的那一刻运行备份。
       - 短期备份是完整备份而不是增量备份。
       - 选择“修改”以更改执行短期备份的时间/日期。

         ![指定短期目标](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. 在“检查磁盘分配”中，检查为 VM 备份提供的磁盘空间。 对于 VM。

   - 建议的磁盘分配基于指定的保留期、工作负荷类型，以及受保护数据的大小。 做出所需的任何更改，然后选择“下一步”。
   - **数据大小：** 保护组中数据的大小。
   - **磁盘空间：** 为保护组建议的磁盘空间量。 若要修改此设置，所分配的总空间应比每个数据源预计增长量略大。
   - **共置数据：** 如果启用共置，受保护的多个数据源可以映射到单个副本和恢复点卷。 并非所有工作负荷都支持归置。
   - **自动增长：** 如果启用此设置，当受保护组中的数据超过初始分配时，Azure 备份服务器会尝试将磁盘大小增加 25%。
   - **存储池详细信息：** 显示存储池的状态，包括总磁盘大小和剩余磁盘大小。

    ![查看磁盘分配](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. 在“选择副本创建方法”页中指定如何创建初始备份，然后选择“下一步” 。
   - 默认设置为“自动通过网络”和“立即”。 
   - 若使用默认设置，则建议指定非高峰时间。 选择“稍后”并指定日期和时间。
   - 如果数据量很大或者网络状态欠佳，请考虑使用可移动介质脱机复制数据。

    ![选择副本创建方法](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. 在“一致性检查选项”中，选择如何以及何时自动执行一致性检查。 然后，选择“下一步”。
      - 当副本数据变得不一致时，可以运行一致性检查；也可以根据设置的计划运行该检查。
      - 如果不想配置自动一致性检查，可运行手动检查。 为此，请右键单击保护组并选择“执行一致性检查”。

1. 在“指定联机保护数据”页中，选择要备份的 VM 或 VM 文件夹。 可以选择单个成员，或者选择“全选”选择所有成员。 然后，选择“下一步”。

    ![指定在线保护数据](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. 在“指定联机备份计划”页中，指定将数据从本地存储备份到 Azure 的频率。

    - 将根据计划生成数据的云恢复点。 然后，选择“下一步”。
    - 生成恢复点后，该恢复点将传输到 Azure 中的恢复服务保管库。

    ![指定联机备份计划](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. 在“指定联机保留策略”页中，指明要在 Azure 中将通过每天/每周/每月/每年备份创建的恢复点保留多长时间。 然后选择“下一步”。

    - 在 Azure 中保留数据的时间长短没有限制。
    - 唯一的限制是每个受保护实例的恢复点不可超过 9999 个。 在本示例中，受保护的实例是 VMware 服务器。

    ![指定联机保留策略](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. 在“摘要”页中检查设置，然后选择“创建组” 。

    ![保护组成员和设置摘要](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-parallel-backups"></a>VMware 并行备份

>[!NOTE]
> 此功能适用于 MABS V3 UR1。

早期版本的 MABS 仅跨保护组执行并行备份。 借助 MABS V3 UR1，单个保护组中的所有 VMWare VM 备份将并行进行，从而提高 VM 备份速度。 所有 VMWare 增量复制作业将并行运行。 默认情况下，并行运行的作业数设置为 8。

你可以如下所示使用注册表项来修改作业数（默认情况下不存在此注册表项，你需要添加它）：

**注册表项路径**：`Software\Microsoft\Microsoft Data Protection Manager\Configuration\ MaxParallelIncrementalJobs\VMWare`<BR>
**注册表项类型**：DWORD（32 位）值。

> [!NOTE]
> 你可以将作业数修改为较高的值。 如果将作业数设置为 1，则复制作业将按顺序运行。 若要将此数量增加到更大的值，则必须考虑 VMWare 性能。 考虑 VMWare vSphere Server 上正在使用的资源数量和所需的额外使用量，并确定要并行运行的增量复制作业的数量。 此外，此更改将仅影响新创建的保护组。 对于现有保护组，你必须临时向保护组中添加另一个 VM。 这会相应地更新保护组配置。 完成此过程后，可以从保护组中删除此 VM。

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

若要备份 vSphere 6.7，请执行以下操作：

- 在 MABS 服务器上启用 TLS 1。2

>[!NOTE]
>VMWare 6.7 及更高版本已启用 TLS 作为通信协议。

- 按如下所示设置注册表项：

```text
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001
```

## <a name="exclude-disk-from-vmware-vm-backup"></a>从 VMware VM 备份中排除磁盘

> [!NOTE]
> 此功能适用于 MABS V3 UR1。

使用 MABS V3 UR1，你可以从 VMware VM 备份中排除特定的磁盘。 配置脚本 **ExcludeDisk.ps1** 位于 `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin folder` 中。

若要配置磁盘排除，请执行以下步骤：

### <a name="identify-the-vmware-vm-and-disk-details-to-be-excluded"></a>识别要排除的 VMWare VM 和磁盘详细信息

  1. 在 VMware 控制台上，转到你要为其排除磁盘的 VM 设置。
  2. 选择要排除的磁盘并记下该磁盘的路径。

        例如，从 TestVM4 中排除硬盘 2 时，硬盘 2 的路径是 **[datastore1] TestVM4/TestVM4\_1.vmdk**。

        ![要排除的硬盘](./media/backup-azure-backup-server-vmware/test-vm.png)

### <a name="configure-mabs-server"></a>配置 MABS 服务器

导航到为 VMware VM 配置了保护的 MABS 服务器，以配置磁盘排除。

  1. 获取在 MABS 服务器上受保护的 VMware 主机的详细信息。

        ```powershell
        $psInfo = get-DPMProductionServer
        $psInfo
        ```

        ```output
        ServerName   ClusterName     Domain            ServerProtectionState
        ----------   -----------     ------            ---------------------
        Vcentervm1                   Contoso.COM       NoDatasourcesProtected
        ```

  2. 选择 VMware 主机，然后列出 VMware 主机的 VM 保护。

        ```powershell
        $vmDsInfo = get-DPMDatasource -ProductionServer $psInfo[0] -Inquire
        $vmDsInfo
        ```

        ```output
        Computer     Name     ObjectType
        --------     ----     ----------
        Vcentervm1  TestVM2      VMware
        Vcentervm1  TestVM1      VMware
        Vcentervm1  TestVM4      VMware
        ```

  3. 选择要为其排除磁盘的 VM。

        ```powershell
        $vmDsInfo[2]
        ```

        ```output
        Computer     Name      ObjectType
        --------     ----      ----------
        Vcentervm1   TestVM4   VMware
        ```

  4. 若要排除磁盘，请导航到 `Bin` 文件夹，并使用以下参数运行 *ExcludeDisk.ps1* 脚本：

        > [!NOTE]
        > 在运行此命令之前，请在 MABS 服务器上停止 DPMRA 服务。 否则，该脚本将返回成功，但不会更新排除列表。 在停止服务之前，请确保没有正在进行的作业。

     **若要在排除中添加/删除磁盘，请运行以下命令：**

      ```powershell
      ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-Add|Remove] "[Datastore] vmdk/vmdk.vmdk"
      ```

     **示例**：

     若要为 TestVM4 添加磁盘排除，请运行以下命令：

       ```powershell
      C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Add "[datastore1] TestVM4/TestVM4\_1.vmdk"
       ```

      ```output
       Creating C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\excludedisk.xml
       Disk : [datastore1] TestVM4/TestVM4\_1.vmdk, has been added to disk exclusion list.
      ```

  5. 验证是否已添加要排除的磁盘。

     **若要查看特定 VM 的现有排除，请运行以下命令：**

        ```powershell
        ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-view]
        ```

     **示例**

        ```powershell
        C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -view
        ```

        ```output
        <VirtualMachine>
        <UUID>52b2b1b6-5a74-1359-a0a5-1c3627c7b96a</UUID>
        <ExcludeDisk>[datastore1] TestVM4/TestVM4\_1.vmdk</ExcludeDisk>
        </VirtualMachine>
        ```

     为此 VM 配置保护后，在保护期间将不会列出已排除的磁盘。

        > [!NOTE]
        > 如果为已受保护的 VM 执行这些步骤，则需在添要排除的磁盘后手动运行一致性检查。

### <a name="remove-the-disk-from-exclusion"></a>从排除中删除磁盘

若要从排除中删除磁盘，请运行以下命令：

```powershell
C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Remove "[datastore1] TestVM4/TestVM4\_1.vmdk"
```

## <a name="next-steps"></a>后续步骤

设置备份时若要排查问题，请查看 [Azure 备份服务器的故障排除指南](./backup-azure-mabs-troubleshoot.md)。
