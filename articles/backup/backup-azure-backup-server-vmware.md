---
title: "使用 Azure 备份服务器备份 VMware 服务器 | Microsoft Docs"
description: "可以使用 Azure 备份服务器将 VMware vCenter/ESXi 服务器备份到 Azure 或磁盘。 本文提供了用于备份（或保护）VMware 工作负荷的分步说明。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: 6b131caf-de85-4eba-b8e6-d8a04545cd9d
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: markgal;
ms.openlocfilehash: ad331dffb7c31d12290f4223967c568e4535fe3c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="back-up-a-vmware-server-to-azure"></a>将 VMware 服务器备份到 Azure

本文介绍如何配置 Azure 备份服务器来保护 VMware 服务器工作负荷。 本文假设已安装 Azure 备份服务器。 如果尚未安装 Azure 备份服务器，请参阅[准备使用 Azure 备份服务器备份工作负荷](backup-azure-microsoft-azure-backup.md)。

Azure 备份服务器可以备份或帮助保护 VMware vCenter 服务器版本 6.5、6.0 和 5.5。


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>与 vCenter 服务器建立安全连接

默认情况下，Azure 备份服务器通过 HTTPS 通道与每个 vCenter 服务器通信。 若要启用安全通信，建议在 Azure 备份服务器上安装 VMware 证书颁发机构 (CA) 证书。 如果不需要安全通信且想去除 HTTPS 要求，请参阅[禁用安全通信协议](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol)。 若要在 Azure 备份服务器与 vCenter 服务器之间创建安全连接，请在 Azure 备份服务器上导入受信任的证书。

通常，在 Azure 备份服务器计算机上使用浏览器通过 vSphere Web 客户端连接到 vCenter 服务器。 首次使用 Azure 备份服务器的浏览器连接到 vCenter 服务器时，连接并不安全。 下图显示了不安全的连接。

![与 VMware 服务器建立的不安全连接示例](./media/backup-azure-backup-server-vmware/unsecure-url.png)

若要解决此问题并创建安全连接，请下载受信任的根 CA 证书。

1. 在 Azure 备份服务器的浏览器中，输入 vSphere Web 客户端的 URL。 此时会显示 vSphere Web 客户端登录页。

    ![vSphere Web 客户端](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    在面向管理员和开发人员的信息底部，找到“下载受信任的根 CA 证书”链接。

    ![“下载受信任的 CA 根证书”链接](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  如果未看到 vSphere Web 客户端登录页，请检查浏览器的代理设置。

2. 单击“下载受信任的根 CA 证书”。

    vCenter 服务器会在本地计算机上下载一个文件。 该文件的名称为 download。 根据所用的浏览器，此时会出现一条消息，询问是要打开还是保存该文件。

    ![下载证书时显示的下载消息](./media/backup-azure-backup-server-vmware/download-certs.png)

3. 请将文件保存到 Azure 备份服务器上的某个位置。 保存文件时，请添加文件扩展名 .zip。

    该文件是一个 .zip 文件，包含有关证书的信息。 通过 .zip 扩展名，可使用提取工具。

4. 右键单击“download.zip”，然后选择“全部提取”来解压缩内容。

    .zip 文件将内容压缩到名为 certs 的文件夹中。 certs 文件夹中包含两种类型的文件。 根证书文件的扩展名以类似 .0 和 .1 的编号序列开头。
    
    CRL 文件的扩展名以类似 .r0 或 .r1 的序列开头。 CRL 文件与证书关联。

    ![在本地解压缩的下载文件 ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. 在 certs 文件夹中，右键单击根证书文件，然后单击“重命名”。

    ![重命名根证书 ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    将根证书的扩展名更改为 .crt。 当被询问是否确定要更改扩展名时，请单击“是”或“确定”。 否则，将更改文件的预期函数。 文件图标更改为表示根证书的图标。

6. 在弹出菜单中右键单击根证书，并选择“安装证书”。

    此时将打开“证书导入向导”对话框。

7. 在“证书导入向导”对话框中，选择“本地计算机”作为证书的目标，然后单击“下一步”继续。

    ![证书存储目标选项 ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    如果系统询问是否要允许更改计算机，请单击“是”或“确定”，以允许进行所有更改。

8. 在“证书存储”页面上，选择“将所有证书放入下列存储”，然后单击“浏览”以选择证书存储。

    ![将证书置于特定的存储位置](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    此时将打开“选择证书存储”对话框。

    ![证书存储文件夹层次结构](./media/backup-azure-backup-server-vmware/cert-store.png)

9. 选择“受信任的根证书颁发机构”作为证书的目标文件夹，然后单击“确定”。

    ![证书目标文件夹](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    “受信任的根证书颁发机构”文件夹确认为证书存储。 单击“下一步”。

    ![证书存储文件夹](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. 在“完成证书导入向导”页上，验证证书是否位于所需的文件夹中，然后单击“完成”。

    ![验证证书是否位于正确的文件夹中](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    随即出现一个对话框，确认证书成功导入。

11. 登录到 vCenter 服务器以确认连接安全。

  如果证书导入失败，且无法建立安全连接，请查阅[获取服务器证书](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html)上的 VMware vSphere 文档。

  如果组织中具有安全边界，且你不想启用 HTTPS 协议，可使用以下过程禁用安全通信。

### <a name="disable-secure-communication-protocol"></a>禁用安全通信协议

如果组织不需要 HTTPS 协议，可使用以下步骤禁用 HTTPS。 若要禁用默认行为，请创建一个用于忽略默认行为的注册表项。

1. 将以下文本复制并粘贴到 .txt 文件中。

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. 将文件保存到 Azure 备份服务器计算机上。 将 DisableSecureAuthentication.reg 作为文件名称输入。

3. 双击该文件激活该注册表项。


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>在 vCenter 服务器上创建角色和用户帐户

在 vCenter 服务器上，角色是一组预定义的权限。 VCenter 服务器管理员创建角色。 若要分配权限，管理员需将用户帐户与一个角色进行配对。 若要建立必需的用户凭据来备份 vCenter 服务器计算机，请创建具有特定特权的角色，然后向该角色关联用户帐户。

Azure 备份服务器使用用户名和密码对 vCenter 服务器进行身份验证。 Azure 备份服务器针对所有备份操作使用这些凭据进行身份验证。

若要为备份管理员添加 vCenter 服务器角色和特权，请：

1. 登录到 vCenter 服务器，然后在 vCenter 服务器的“导航器”面板中单击“管理”。

    ![VCenter 服务器导航器面板中的管理选项](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. 在“管理”中选择“角色”，然后在“角色”面板中单击“添加角色”图标（加号）。

    ![添加角色](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    此时将显示“创建角色”对话框。

    ![创建角色](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. 在“创建角色”对话框中的“角色名称”字段内，键入 *BackupAdminRole*。 角色名称可以是所需的任何内容，但它应该能够帮助识别该角色。

4. 为相应的 vCenter 版本选择特权，然后单击“确定”。 下表列出了 vCenter 6.0 和 vCenter 5.5 所需的特权。

  选择特权时，请单击父标签旁的图标展开父级并查看子特权。 若要选择的 VirtualMachine 去权限，需跳转几个级别转到父子层次结构。 不需要选择父特权中的所有子特权。

  ![父子权限层次结构](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  单击“确定”后，“角色”面板的列表中将显示新角色。

|vCenter 6.0 的特权| vCenter 5.5 的特权|
|--------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>创建 vCenter 服务器用户帐户和权限

创建带特权的角色后，请创建用户帐户。 用户帐户具有名称和密码（提供用于身份验证的凭据）。

1. 若要创建用户帐户，请在 vCenter 服务器的“导航器”面板中，单击“用户和组”。

    ![用户和组选项](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    此时将显示“vCenter用户和组”面板。

    ![vCenter 用户和组面板](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. 在“vCenter 用户和组”面板中，选择“用户”选项卡，然后单击“添加用户”图标（加号）。

    将显示“新用户”对话框。

3. 在“新用户”对话框框中，添加用户的信息，然后单击“确定”。 在此过程中，用户名是 BackupAdmin。

    ![“新用户”对话框](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    新用户帐户随即显示在列表中。

4. 若要将用户帐户与角色关联，请在“导航器”面板中单击“全局权限”。 在“全局权限”面板中选择“管理”选项卡，然后单击“添加”图标（加号）。

    ![全局权限面板](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    此时将打开“全局权限 Root - 添加权限”对话框。

5. 在“全局权限 Root - 添加权限”对话框中，单击“添加”以选择用户或组。

    ![选择用户或组](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    此时将打开“选择用户/组”对话框。

6. 在“选择用户/组”对话框中选择“BackupAdmin”，然后单击“添加”。

    在“用户”中，用户帐户采用“域\用户名”格式。 若要使用其他域，请从“域”列表中选择该域。

    ![添加 BackupAdmin 用户](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    单击“确定”，将选定的用户添加到“添加权限”对话框中。

7. 确定用户后，请将该用户分配到角色。 在“分配的角色”的下拉列表中，选择“BackupAdminRole”，然后单击“确定”。

    ![向角色分配用户](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  新用户帐户和关联的角色将显示在“全局权限”面板的“管理”选项卡上的列表中。


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>在 Azure 备份服务器上建立 vCenter 服务器凭据

在将 VMware 服务器添加到 Azure 备份服务器之前，请安装 [Azure 备份服务器更新 1](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server)。

1. 若要打开 Azure 备份服务器，请双击 Azure 备份服务器桌面上的图标。

    ![Azure 备份服务器图标](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    如果在桌面上找不到该图标，可从已安装应用列表打开 Azure 备份服务器。 Azure 备份服务器的应用名称为“Microsoft Azure 备份”。

2. 在 Azure 备份服务器控制台中，依次单击“管理”和“生产服务器”，然后在工具功能区中单击“管理 VMware”。

    ![Azure 备份服务器控制台](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    此时将显示“管理证书”对话框。

    ![Azure 备份服务器的“管理凭据”对话框](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. 在“管理凭据”对话框中，单击“添加”来打开“添加凭据”对话框。

4. 在“添加凭据”对话框中，输入新凭据的名称和说明。 然后指定用户名和密码。 名称 *Contoso Vcenter credential* 用于标识下一过程中的凭据。 用户名和密码必须与 vCenter 服务器使用的相同。 如果 vCenter 服务器和 Azure 备份服务器不在同一域中，请在“用户名”中指定域。

    ![Azure 备份服务器的“添加凭据”对话框](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    单击“添加”，将新凭据添加到 Azure 备份服务器。 新凭据随即在“管理凭据”对话框的列表中显示。
    
    ![Azure 备份服务器的“管理凭据”对话框](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. 若要关闭“管理凭据”对话框，请单击右上角的“X”。


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>将 vCenter 服务器添加到 Azure 备份服务器

生产服务器添加向导用于将 vCenter 服务器添加到 Azure 备份服务器。

若要打开生产服务器添加向导，请完成以下过程：

1. 在 Azure 备份服务器控制台中，依次单击“管理”、“生产服务器”和“添加”。

    ![打开生产服务器添加向导](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    此时将打开“生产服务器添加向导”对话框。

    ![生产服务器添加向导](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. 在“选择生产服务器类型”页面上，选择“VMware 服务器”，然后单击“下一步”。

3. 在“服务器名称/IP 地址”中，指定 VMware 服务器的完全限定域名 (FQDN) 或 IP 地址。 如果所有 ESXi 服务器由同一个 vCenter 管理，则可使用 vCenter 名称。

    ![指定 VMware 服务器 FQDN 或 IP 地址](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. 在“SSL 端口”中，输入用于与 VMware 服务器通信的端口。 除非确定需要使用其他端口，否则请使用默认端口 443。

5. 在“指定凭据”中，选择先前创建的凭据。

    ![指定凭据](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. 单击“添加”将 VMware 服务器添加到“添加的 VMware 服务器”列表，然后单击“下一步”转到向导中的下一页面。

    ![添加 VMWare 服务器和凭据](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. 在“摘要”页面中单击“添加”，将指定的 VMware 服务器添加到 Azure 备份服务器。

    ![将 VMware 服务器添加到 Azure 备份服务器](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  VMware 服务器备份不使用代理，且即刻即可添加新服务器。 “完成”页面将显示结果。

  ![“完成”页面](./media/backup-azure-backup-server-vmware/summary-screen.png)

  若要将多个 vCenter 服务器实例添加到 Azure 备份服务器，请重复本部分中的上述步骤。

将 vCenter 服务器添加到 Azure 备份服务器后，接下来是创建保护组。 保护组指定有关短期或长保留的各种详细信息，并且是定义和应用备份策略的位置。 备份策略是规定何时创建备份以及要备份哪些内容的计划。


## <a name="configure-a-protection-group"></a>配置保护组

如果从未用过 System Center Data Protection Manager 或 Azure 备份服务器，请参阅[规划磁盘备份](https://technet.microsoft.com/library/hh758026.aspx)来准备硬件环境。 确认拥有适当的存储后，使用“新建保护组”向导添加 VMware 虚拟机。

1. 在 Azure 备份服务器控制台中单击“保护”，并在工具功能区中，单击“新建”打开“创建新保护组”向导。

    ![打开“新建保护组”向导](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    此时将打开“新建保护组”向导对话框。

    ![“新建保护组”向导对话框](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    单击“下一步”转到“选择保护组类型”页面。

2. 在“选择保护组类型”页面上选择“服务器”，然后单击“下一步”。 将出现“选择组成员”页面。

3. 在“选择组成员”页面上，将显示可用成员和所选成员。 选择要保护的成员，然后单击“下一步”。

    ![选择组成员](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    选择成员时，如果选择包含其他文件夹或 VM 的文件夹，则这些子文件夹和 VM 一并选中。 在父文件夹中包含文件夹和 VM 称为文件夹级保护。 若要删除文件夹或 VM，请清除复选框。

    如果某个 VM 或者包含某个 VM 的文件夹已在 Azure 中受保护，则无法再次选择该 VM。 也就是说，在 Azure 中保护某个 VM 后，无法再次对它进行保护，由此防止从一个 VM 创建重复的恢复点。 若要查看哪个 Azure 备份服务器实例已保护成员，请指向该成员，查看保护服务器的名称。

4. 在“选择数据保护方法”页面上，输入保护组的名称。 将选择短期保护（在磁盘中）和联机保护。 如果想要使用联机保护（在 Azure 中），必须使用在磁盘中进行短期保护。 单击“下一步”，继续提供短期保护时间范围信息。

    ![选择数据保护方法](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. 在“指定短期目标”屏幕上，为“保留期”指定*磁盘中存储的*恢复点的保留天数。 如果想要更改恢复点的创建时间和日期，请单击“修改”。 短期恢复点是完整备份， 而不是增量备份。 如果对短期目标感到满意，请单击“下一步”。

    ![指定短期目标](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. 在“检查磁盘分配”页面上，检查 VM 的磁盘空间并根据需要进行修改。 建议的磁盘分配基于“指定短期目标”页面中指定的保留期、工作负荷的类型、以及受保护数据的大小（在步骤 3 中确定）。  

  - **数据大小：**保护组中数据的大小。
  - **磁盘空间：**为保护组建议的磁盘空间量。 若要修改此设置，所分配的总空间应比每个数据源预计增长量略大。
  - **归置数据：**如果启用归置，受保护的多个数据源可以映射到单个副本和恢复点卷。 并非所有工作负荷都支持归置。
  - **自动增长：**启用此设置后，当受保护组中的数据增长量超过初始分配时，System Center Data Protection Manager 会尝试将磁盘大小增加 25%。
  - **存储池详细信息：**显示存储池的状态，包括总磁盘大小和剩余磁盘大小。

    ![查看磁盘分配](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    如果对空间分配感到满意，请单击“下一步”。

7. 在“选择副本创建方法”页面上，指定如何为 Azure 备份服务器上受保护数据生成初始副本。

    默认设置为“自动通过网络”和“立即”。 若使用默认设置，则建议指定非高峰时间。 选择“稍后”并指定日期和时间。

    如果数据量很大或者网络状态欠佳，请考虑使用可移动介质脱机复制数据。

    做出选择后，请单击“下一步”。

    ![选择副本创建方法](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. 在“一致性检查选项”页面上，选择如何以及何时自动执行一致性检查。 当副本数据变得不一致时，可以运行一致性检查；也可以根据设置的计划运行该检查。

    如果不想配置自动一致性检查，可运行手动检查。 在 Azure 备份服务器控制台的保护区域中，右键单击保护组，然后选择“执行一致性检查”。

    单击“下一步”转到下一页面。

9. 在“指定在线保护数据”页面上，选择一个或多个要保护的数据源。 可以选择单个成员，或者单击“全选”选择所有成员。 选择成员后，单击“下一步”。

    ![指定在线保护数据](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. 在“指定联机备份计划”页面上，指定用于从磁盘备份生成恢复点的计划。 生成恢复点后，该恢复点将传输到 Azure 中的恢复服务保管库。 如果对联机备份计划感到满意，请单击“下一步”。

    ![指定联机备份计划](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. 在“指定联机保留策略”页面上，指定备份数据要在 Azure 中保留的时长。 定义策略后，单击“下一步”。

    ![指定联机保留策略](./media/backup-azure-backup-server-vmware/retention-policy.png)

    在 Azure 中保留数据的时间长短没有限制。 在 Azure 中存储恢复点数据时，唯一的限制是每个受保护数据所具备的恢复点不可超过 9999 个。 在本示例中，受保护的实例是 VMware 服务器。

12. 在“摘要”页面上，查看保护组成员和设置的详细信息，然后单击“创建组”。

    ![保护组成员和设置摘要](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>后续步骤
如果使用 Azure 备份服务器保护 VMware 工作负荷，就可能有兴趣使用该服务器来帮助保护 [Microsoft Exchange Server](./backup-azure-exchange-mabs.md)、[Microsoft SharePoint 场](./backup-azure-backup-sharepoint-mabs.md)或 [SQL Server数据库](./backup-azure-sql-mabs.md)。

若要了解代理注册、保护组配置或作业备份方面的问题，请参阅[排查 Azure 备份服务器问题](./backup-azure-mabs-troubleshoot.md)。
