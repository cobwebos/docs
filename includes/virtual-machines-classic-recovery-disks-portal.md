如果虚拟机 (VM) 在 Azure 中遇到启动或磁盘错误，可能需要对虚拟硬盘本身执行故障排除步骤。 一个常见示例是应用程序更新失败，使 VM 无法成功启动。 本文介绍如何使用 Azure 门户将虚拟硬盘连接到另一个 VM 来修复所有错误，然后重新创建原始 VM。


## <a name="recovery-process-overview"></a>恢复过程概述
故障排除过程如下：

1. 删除遇到问题的 VM，但保留虚拟硬盘。
2. 将虚拟硬盘附加并装入到另一个 VM，以便进行故障排除。
3. 连接到故障排除 VM。 编辑文件或运行工具以修复原始虚拟硬盘上的错误。
4. 从故障排除 VM 卸载并分离虚拟硬盘。
5. 使用原始虚拟硬盘创建 VM。

## <a name="delete-the-original-vm"></a>删除原始 VM
虚拟硬盘和 VM 在 Azure 中是两个不同的资源。 虚拟硬盘是存储操作系统、应用程序和配置的地方。 VM 只是定义大小或位置的元数据，引用虚拟硬盘或虚拟网络接口卡 (NIC) 等资源。 每个虚拟硬盘在附加到 VM 时分配有一个租约。 尽管 VM 正在运行时也可以附加和分离数据磁盘，但是，若要分离 OS 磁盘，则必须删除 VM 资源。 即使 VM 处于停止和解除分配状态，租约也会继续将 OS 磁盘关联到该 VM。

恢复 VM 的第一步是删除 VM 资源本身。 删除 VM 时会将虚拟硬盘留在存储帐户中。 删除 VM 后，可将虚拟硬盘附加到另一个 VM，以排查和解决错误。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 在左侧菜单中单击“虚拟机(经典)”。
3. 选择有问题的 VM，单击“磁盘”，然后标识虚拟硬盘的名称。 
4. 选择 OS 虚拟硬盘，然后查看“位置”以标识包含该虚拟硬盘的存储帐户。 在以下示例中，“.blob.core.windows.net”之前（紧邻）的字符串为存储帐户名称。

    ```
    https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd
    ```

    ![有关 VM 位置的映像](./media/virtual-machines-classic-recovery-disks-portal/vm-location.png)

5. 右键单击 VM，然后选择“删除”。 请确保在删除 VM 时磁盘未处于选中状态。
6. 创建新的恢复 VM。 此 VM 必须与问题 VM 位于同一区域和资源组（云服务）。
7. 选择恢复 VM，然后选择“磁盘” > “附加现有磁盘”。
8. 若要选择现有的虚拟硬盘，请单击“VHD 文件”：

    ![浏览现有 VHD](./media/virtual-machines-classic-recovery-disks-portal/select-vhd-location.png)

9. 选择存储帐户 > VHD 容器 > 虚拟硬盘，单击“选择”按钮确认所做选择。

    ![选择现有 VHD](./media/virtual-machines-classic-recovery-disks-portal/select-vhd.png)

10. 选择 VHD 后，再选择“确定”附加现有虚拟硬盘。
11. 几秒钟后，VM 的“磁盘”窗格会显示作为数据磁盘连接的现有虚拟硬盘：

    ![现有虚拟硬盘已附加为数据磁盘](./media/virtual-machines-classic-recovery-disks-portal/attached-disk.png)

## <a name="fix-issues-on-the-original-virtual-hard-disk"></a>修复原始虚拟硬盘上的问题
装载现有虚拟硬盘后，即可根据需要执行任何维护和故障排除步骤。 解决问题后，请继续执行以下步骤。

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>卸载并分离原始虚拟硬盘
解决错误后，可从故障排除 VM 中卸载并分离现有虚拟硬盘。 在将虚拟硬盘附加到故障排除 VM 的租约释放前，不能将该虚拟硬盘与任何其他 VM 一起使用。  

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 在左侧菜单中选择“虚拟机(经典)”。
3. 找到恢复 VM。 选择“磁盘”，右键单击该磁盘，然后选择“分离”。

## <a name="create-a-vm-from-the-original-hard-disk"></a>从原始硬盘创建 VM

若要从原始虚拟硬盘创建 VM，请使用 [Azure 门户](https://portal.azure.com)。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在门户左上角，选择“创建资源” > “计算” > “虚拟机” > “从库中”。
3. 在“选择映像”部分，选择“我的磁盘”，然后选择原始虚拟硬盘。 查看位置信息。 这是必须在其中部署 VM 的区域。 选择“下一步”按钮。
4. 在“虚拟机配置”部分，键入 VM 名称并选择 VM 大小。
