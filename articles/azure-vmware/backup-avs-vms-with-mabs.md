---
title: 备份具有 Azure 备份服务器的 Azure VMware 解决方案 Vm
description: 使用 Azure 备份服务器配置 Azure VMware 解决方案环境以备份虚拟机。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 4cba224de3d8b223ebcc1ac4d2d8d569275b4e3b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272240"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>备份具有 Azure 备份服务器的 Azure VMware 解决方案 Vm

在本文中，我们将逐步介绍如何使用 Azure 备份服务器在 Azure VMware 解决方案上备份 (Vm) 的 VMware 虚拟机。 在开始之前，请确保全面了解如何 [设置适用于 Azure VMware 解决方案的 Microsoft Azure 备份服务器](set-up-mabs-for-avs.md)。

接下来，我们将演练所有必要的过程，以便：

> [!div class="checklist"] 
> * 设置一个安全通道，使 Azure 备份服务器能够通过 HTTPS 来与 VMware 服务器通信。 
> * 将帐户凭据添加到 Azure 备份服务器。 
> * 将 vCenter 添加到 Azure 备份服务器。 
> * 设置一个包含要备份的 VMware VM 的保护组，指定备份设置，并计划备份。

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>创建到 vCenter 服务器的安全连接

默认情况下，Azure 备份服务器通过 HTTPS 来与 VMware 服务器通信。 若要设置 HTTPS 连接，请下载 VMware 证书颁发机构 (CA) 证书，并将其导入 Azure 备份服务器上。

### <a name="set-up-the-certificate"></a>设置证书

1. 在浏览器的 Azure 备份服务器计算机上，输入 vSphere Web 客户端 URL。

   > [!NOTE] 
   > 如果未显示 "VMware **入门** " 页，请验证连接和浏览器代理设置，然后重试。

1. 在 "VMware **入门** " 页上，选择 " **下载受信任的根 CA 证书**"。

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere Web 客户端":::

1. 将 **download.zip** 文件保存到 Azure 备份服务器计算机，然后将其内容提取到 **证书** 文件夹，其中包含：

   - 根证书文件，其扩展名以编号序列（如 .0 和. 1）开头。
   - 其扩展名以类似于 r0 或 r1 的序列开头的 CRL 文件。

1. 在 " **证书** " 文件夹中，右键单击根证书文件，然后选择 " **重命名** "，将扩展名更改为 **.crt**。

   文件图标将更改为表示根证书的图标。

1. 右键单击根证书，并选择 " **安装证书**"。

1. 在 **证书导入向导**中，选择 " **本地计算机** " 作为证书的目标，然后选择 " **下一步**"。

   ![向导欢迎页](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > 如果需要，请确认是否允许对计算机进行更改。

1. 选择 **"将所有证书放入下列存储**"，然后选择 " **浏览** " 以选择证书存储。

   ![证书存储](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. 选择 " **受信任的根证书颁发机构** " 作为目标文件夹，然后选择 **"确定"**。

1. 查看设置，然后选择 " **完成** " 以开始导入证书。

   ![验证证书是否位于正确的文件夹中](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. 确认证书导入后，登录到 vCenter 服务器以确认连接安全。

### <a name="enable-tls-12-on-azure-backup-server"></a>在 Azure 备份服务器上启用 TLS 1。2

VMware 6.7 之前已启用 TLS 作为通信协议。 

1. 复制以下注册表设置，并将其粘贴到记事本中。 然后将该文件另存为 TLS。不带 .txt 扩展名的 REG。

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

1. 右键单击 TLS。注册文件，然后选择 " **合并** " 或 " **打开** " 以将设置添加到注册表中。

## <a name="add-the-provisioning-ip-address"></a>添加预配 IP 地址 

Azure VMware 解决方案不会从虚拟网络中部署的 VM 解析 ESX 主机。 需要执行附加步骤，以便在 Azure 备份服务器 VM 上添加主机文件条目。

### <a name="identify-the-ip-address-for-esxi-hosts"></a>确定 ESXi 主机的 IP 地址

1. 打开浏览器并登录到 vCenter Url。 

   > [!TIP]
   > 你可以在 " [连接到私有云的本地 vCenter](tutorial-access-private-cloud.md#connect-to-the-local-vcenter-of-your-private-cloud)" 中找到 url。

1. 在 vSphere 客户端中，选择你计划为其启用备份的群集。

   :::image type="content" source="media/avs-backup/vsphere-client-select-host.png" alt-text="VSphere 客户端-选择主机":::

1. 选择 "**配置**  >  **网络**  >  **VMKernel 适配器**"。 在设备列表下，确定启用了 **设置** 角色的网络适配器。 记下 **IP 地址** 和 ESXi 主机名。

   :::image type="content" source="media/avs-backup/vmkernel-adapters-provisioning-enabled.png" alt-text="VMKernel 适配器-预配已启用设备":::

1. 对计划为其启用备份的每个群集下的每个 ESXi 主机重复前面的步骤。

### <a name="update-the-host-file-on-azure-backup-server"></a>更新 Azure 备份服务器上的主机文件

1. 以管理员身份打开记事本。

1. 选择**File**  >  "**打开**文件"，然后搜索 c:\Windows\System32\Drivers\etc\hosts。

1. 为每个 ESXi 主机添加条目，以及在上一节中标识的 IP 地址。

1. 保存更改，然后关闭记事本。

## <a name="add-the-account-on-azure-backup-server"></a>在 Azure 备份服务器上添加帐户

1. 打开 Azure 备份服务器，然后在 Azure 备份服务器控制台中，选择 "**管理**" "  >  **生产服务器**" "管理  >  **VMware**"。

   ![Azure 备份服务器控制台](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. 在 " **管理凭据** " 对话框中，选择 " **添加**"。

   ![在 "管理凭据" 对话框中，选择 "添加"。](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. 在“添加凭据”对话框中，输入新凭据的名称和说明。**** 指定你在 VMware 服务器上定义的用户名和密码。

   > [!NOTE] 
   > 如果 VMware 服务器和 Azure 备份服务器不在同一个域中，请在 " **用户名** " 框中指定域。

   ![Azure 备份服务器的“添加凭据”对话框](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. 选择 " **添加** " 以添加新凭据。

   ![屏幕截图显示 "Azure 备份服务器管理凭据" 对话框，其中显示了新凭据。](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>将 vCenter 服务器添加到 Azure 备份服务器

1. 在 Azure 备份服务器控制台中，选择 "**管理**  >  **生产服务器**  >  **添加**"。

   ![打开生产服务器添加向导](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. 选择 " **VMware 服务器**"，然后选择 " **下一步**"。

   ![生产服务器添加向导](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. 指定 vCenter 的 IP 地址。

   ![指定 VMware 服务器](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. 在 " **SSL 端口** " 框中，输入用于与 vCenter 通信的端口。

   > [!TIP] 
   > 端口443是默认端口，但如果 vCenter 侦听其他端口，则可以对其进行更改。

1. 在 " **指定凭据** " 框中，选择在上一部分中创建的凭据。

1. 选择 " **添加** " 将 vCenter 添加到 "服务器" 列表，然后选择 " **下一步**"。

   ![添加 VMware 服务器和凭据](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. 在 " **摘要** " 页上，选择 " **添加** " 以将 vCenter 添加到 Azure 备份服务器。

   新服务器立即添加。 vCenter 不需要代理。

   ![将 VMware 服务器添加到 Azure 备份服务器](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. 在 " **完成** " 页上，查看设置，然后选择 " **关闭**"。

   ![“完成”页](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   应会看到 " **生产服务器** " 下列出的 vCenter 服务器，其类型为 " **VMware 服务器** "，" **代理状态** " 显示为 **"正常"**。 

   >[!TIP]
   >如果将 " **代理状态** " 显示为 " **未知**"，请选择 " **刷新**"。

## <a name="configure-a-protection-group"></a>配置保护组

保护组收集多个 VM，并将相同的数据保留和备份设置应用到组中的所有 VM。

1. 在 Azure 备份服务器控制台中，选择 "**保护**" "  >  **新建**"。

   ![打开“创建新保护组”向导](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. 在 " **创建新保护组** 向导" 欢迎页上，选择 " **下一步**"。

   ![“创建新保护组”向导对话框](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. 在“选择保护组类型”页上选择“服务器”，然后选择“下一步”。**** **** **** 此时将显示 " **选择组成员** " 页。

1. 在 " **选择组成员** " 页上，选择要备份的 vm (或 vm 文件夹) ，然后选择 " **下一步**"。

   > [!NOTE]
   > 选择文件夹或 Vm 时，还会选择该文件夹中的文件夹进行备份。 可以取消选中不想要备份的文件夹或 VM。 如果 VM 或文件夹已备份，则无法选择它，这可确保不会为 VM 创建重复的恢复点。

   ![选择组成员](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. 在 " **选择数据保护方法** " 页上，输入保护组的名称和保护设置。 

1. 将短期保护设置为 **磁盘**，启用在线保护，然后选择 " **下一步**"。

   ![选择数据保护方法](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. 指定要将数据备份到磁盘的时间长度。

   - **保持期**：保留磁盘恢复点的天数。
   - **快速完整备份**：创建磁盘恢复点的频率。 若要更改短期备份发生的时间或日期，请选择 " **修改**"。

   :::image type="content" source="media/avs-backup/new-protection-group-specify-short-term-goals.png" alt-text="指定基于磁盘的保护的短期目标":::

1. 在 " **查看磁盘存储分配** " 页上，查看为 VM 备份提供的磁盘空间。

   - 建议的磁盘分配基于指定的保留期、工作负荷类型，以及受保护数据的大小。 进行任何所需的更改，然后选择 " **下一步**"。
   - **数据大小：** 保护组中数据的大小。
   - **磁盘空间：** 建议用于保护组的磁盘空间量。 如果要修改此设置，则分配的总空间应略大于你估计每个数据源的增长量。
   - **存储池详细信息：** 显示存储池的状态，包括总磁盘大小和剩余磁盘大小。

   :::image type="content" source="media/avs-backup/review-disk-allocation.png" alt-text="查看存储池中分配的磁盘空间":::

   > [!NOTE]
   > 在某些情况下，报告的数据大小高于实际 VM 大小。 我们注意到该问题，当前正在调查此问题。

1. 在 " **选择副本创建方法** " 页上，指明要如何进行初始备份，然后选择 " **下一步**"。

   - 默认设置为“自动通过网络”和“立即”。  如果使用默认值，请指定非高峰时间。 如果选择 " **以后**"，请指定日期和时间。
   - 如果数据量很大或者网络状态欠佳，请考虑使用可移动介质脱机复制数据。

   ![选择副本创建方法](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. 对于 **一致性检查选项**，请选择如何以及何时自动执行一致性检查，然后选择 " **下一步**"。

   - 当副本数据变得不一致时，可以运行一致性检查；也可以根据设置的计划运行该检查。
   - 如果不想配置自动一致性检查，则可以通过右键单击保护组 " **执行一致性检查**" 来运行手动检查。

1. 在 " **指定联机保护数据** " 页上，选择要备份的 VM 或 vm 文件夹，然后选择 " **下一步**"。 

   > [!TIP]
   > 可以逐个选择成员，也可以选择 " **全选** " 选择所有成员。

   ![指定在线保护数据](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. 在 " **指定联机备份计划** " 页上，指定要将数据从本地存储备份到 Azure 的频率，然后选择 " **下一步**"。 

   - 要根据计划生成的数据的云恢复点。 
   - 生成恢复点后，该恢复点将传输到 Azure 中的恢复服务保管库。

   ![指定联机备份计划](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. 在 " **指定联机保留策略** " 页上，指定要将从每日、每周、每月或每年备份创建的恢复点保留多长时间到 Azure，然后选择 " **下一步**"。

   - 在 Azure 中保留数据的时间长短没有限制。
   - 唯一的限制是每个受保护实例的恢复点数不能超过9999个。 在本示例中，受保护的实例是 VMware 服务器。

   ![指定联机保留策略](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. 在 " **摘要** " 页上，查看设置，然后选择 " **创建组**"。

   ![保护组成员和设置摘要](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>监视 Azure 备份服务器控制台

将保护组配置为备份 Azure VMware 解决方案 Vm 后，可以使用 Azure 备份服务器控制台监视备份作业和警报的状态。 下面是可以监视的内容。

- 在 "**监视**" 窗格中的 "**警报**" 选项卡上，可以监视保护组、特定受保护计算机或消息严重性的错误、警告和一般信息。 你可以查看活动警报和非活动警报，并设置电子邮件通知。
- 在 "**监视**" 窗格中的 "**作业**" 选项卡上，可以查看 Azure 备份服务器为特定受保护的数据源或保护组启动的作业。 你可以跟踪作业进度或检查作业使用的资源。
- 在 " **保护** " 任务区域中，你可以检查保护组中的卷和共享的状态。 你还可以检查配置设置，如恢复设置、磁盘分配和备份计划。
- 在 " **管理** " 任务区域中，可以查看 " **磁盘"、"联机**" 和 " **代理** " 选项卡，以检查存储池中的磁盘的状态、注册到 Azure 并部署 DPM 代理状态。

:::image type="content" source="media/avs-backup/monitor-backup-jobs-in-mabs.png" alt-text="监视 Azure 备份服务器中备份作业的状态":::

## <a name="restore-vmware-virtual-machines"></a>还原 VMware 虚拟机

在 Azure 备份服务器管理员控制台中，有两种方法可用于查找可恢复数据。 您可以搜索或浏览。 在恢复数据时，你可能或不希望将数据或 VM 还原到同一位置。 出于此原因，Azure 备份服务器支持用于 VMware VM 备份的三个恢复选项：

- **原始位置恢复 (OLR) **：使用 OLR 将受保护的 VM 还原到其原始位置。 仅当备份发生之后未添加或删除任何磁盘时，才可以将 VM 还原到其原始位置。 如果添加或删除了磁盘，则必须使用备用位置恢复。
- **备用位置恢复 (ALR) **：缺少原始 vm，或者不希望扰乱原始 vm 时，请将 VM 恢复到备用位置。 若要将 VM 恢复到备用位置，必须提供 ESXi 主机、资源池、文件夹和存储数据存储及路径的位置。 为帮助区分已还原的 VM 与原始 VM，Azure 备份服务器将 "恢复" 追加到 VM 的名称。
- **单个文件位置恢复 (ILR) **：如果受保护的 Vm 是 WINDOWS Server VM，则可以使用 AZURE 备份服务器的 ILR 功能来恢复 VM 内的单个文件或文件夹。 若要恢复单个文件，请参阅本文后面部分介绍的过程。 从 VM 还原单个文件仅适用于 Windows VM 和磁盘恢复点。

### <a name="restore-a-recovery-point"></a>还原恢复点

1. 在 Azure 备份服务器管理员控制台中，选择 " **恢复** " 视图。 

1. 在 " **浏览** " 窗格中，浏览或筛选以查找要恢复的 VM。 选择 VM 或文件夹后，将显示可用的恢复点。

   ![可用的恢复点](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. 在 " **恢复点** " 窗格中，使用 "日历" 和下拉菜单来选择执行恢复点的日期。 以粗体显示的日历日期具有可用的恢复点。 或者，您可以右键单击 VM，然后选择 " **显示所有恢复点** "，然后从列表中选择恢复点。

   > [!NOTE] 
   > 对于短期保护，选择基于磁盘的恢复点以实现更快的恢复。 短期恢复点过期后，你将仅看到要恢复的 **联机** 恢复点。

1. 从联机恢复点恢复之前，请确保暂存位置包含足够的可用空间来容纳你要恢复的 VM 的完全未压缩大小。 可以通过运行 **配置订阅设置向导**来查看或更改暂存位置。

   :::image type="content" source="media/avs-backup/mabs-recovery-folder-settings.png" alt-text="Azure 备份服务器恢复文件夹设置":::

1. 选择 " **恢复** " 打开 " **恢复向导**"。

   ![恢复向导，"复查恢复选择" 页](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. 选择 " **下一步** " 以前往 " **指定恢复选项** " 屏幕。 
1. 再次选择 " **下一步** " 以切换到 " **选择恢复类型** " 屏幕。 

   > [!NOTE]
   > VMware 工作负荷不支持启用网络带宽限制。

1. 在 " **选择恢复类型** " 页上，选择 "恢复到原始实例或新位置"，然后选择 " **下一步**"。

   - 如果选择“恢复到原始实例”，则无需在向导中做出任何更多选择。 会使用原始实例的数据。
   - 如果选择“在任何主机上作为虚拟机恢复”，则在“指定目标”屏幕上，为“ESXi 主机”、“资源池”、“文件夹”和“路径”提供相关信息。

   ![选择恢复类型页](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. 在 " **摘要** " 页上，查看设置，然后选择 " **恢复** " 以启动恢复过程。 

   “恢复状态”屏幕显示恢复操作的进度。

### <a name="restore-an-individual-file-from-a-vm"></a>从 VM 中还原单个文件

可以从受保护的 VM 恢复点还原单个文件。 此功能仅适用于 Windows Server VM。 还原单个文件与还原整个 VM 类似，只是在开始恢复过程之前，浏览到 VMDK 并找到所需的文件。 

> [!NOTE]
> 从 VM 还原单个文件仅适用于 Windows VM 和磁盘恢复点。

1. 在 Azure 备份服务器管理员控制台中，选择 " **恢复** " 视图。

1. 在 " **浏览** " 窗格中，浏览或筛选以查找要恢复的 VM。 选择 VM 或文件夹后，将显示可用的恢复点。

   ![恢复点可用](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. 在 " **恢复点** " 窗格中，使用日历选择包含所需恢复点的日期。 根据备份策略的配置方式，日期可以有多个恢复点。 

1. 选择恢复点的拍摄日期后，请确保选择正确的 **恢复时间**。 

   > [!NOTE]
   > 如果所选日期具有多个恢复点，则在“恢复时间”下拉菜单中选择适当的恢复点。 

   选择恢复点后，可恢复项目的列表将显示在 " **路径** " 窗格中。

1. 若要查找要恢复的文件，请在 " **路径** " 窗格中，双击 " **可恢复项目** " 列中的项以将其打开。 然后选择要恢复的一个或哪些文件夹。 若要选择多个项，请在选择每个项时选择 **Ctrl** 键。 使用 " **路径** " 窗格搜索出现在 " **可恢复项目** " 列中的文件或文件夹的列表。
    
   > [!NOTE]
   > **下面的搜索列表** 不搜索子文件夹。 若要搜索子文件夹，请双击文件夹。 使用“向上”按钮将子文件夹移动到父文件夹。 你可以选择多个项目（文件和文件夹），但它们必须在同一个父文件夹中。 无法从同一恢复作业的多个文件夹中恢复项。

   ![复查恢复选择](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. 选择要恢复的项目后，在管理员控制台工具功能区中，选择 " **恢复** " 打开 " **恢复向导**"。 在 **恢复向导**中，" **复查恢复选择** " 屏幕会显示要恢复的选定项目。

1. 在 " **指定恢复选项** " 屏幕上，执行以下操作之一：

   - 选择 " **修改** " 以启用网络带宽限制。 在 " **限制** " 对话框中，选择 " **启用网络带宽使用限制** " 以将其打开。 启用后，配置“设置”和“工作计划”。
   - 选择 " **下一步** "，使网络阻止处于禁用状态。

1. 在 " **选择恢复类型** " 屏幕上，选择 " **下一步**"。 只能将文件或文件夹恢复到网络文件夹。

1. 在 " **指定目标** " 屏幕上，选择 " **浏览** " 查找文件或文件夹的网络位置。 Azure 备份服务器创建一个将所有已恢复项复制到其中的文件夹。 文件夹名称的前缀 MABS_day 月。 选择恢复的文件或文件夹的位置时，将提供该位置的详细信息，例如 **目标**、 **目标路径**和 **可用空间**。

   ![指定恢复文件的位置](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. 在“指定恢复选项”屏幕上，选择要应用的安全设置。 你可以选择修改网络带宽使用限制，但默认情况下限制处于禁用状态。 此外，不会启用 **SAN 恢复** 和 **通知** 。

1. 在 " **摘要** " 屏幕上，查看设置，然后选择 " **恢复** " 以启动恢复过程。 “恢复状态”屏幕显示恢复操作的进度。

## <a name="next-steps"></a>后续步骤

设置备份时若要排查问题，请查看 Azure 备份服务器的故障排除指南。

> [!div class="nextstepaction"]
> [Azure 备份服务器疑难解答指南](../backup/backup-azure-mabs-troubleshoot.md)
