

## <a name="multi-and-single-instance-vms"></a>多和单实例虚拟机
在上运行 Azure 计数它关键时其虚拟机进行计划内的维护停机时间-由于可以计划的许多客户大约 15 分钟-发生这种情况在维护期间。 你可以使用可用性集可帮助控制当已设置的 Vm 收到计划内的维护。

有两个可能的配置，在 Azure 上运行的虚拟机。 请将 Vm 配置为多实例或单实例。 如果 Vm 在可用性集中，然后它们被配置为多实例。 请注意，即使单一 Vm 将部署在可用性集中时，因此，它们被视为多实例。 如果 Vm 不在可用性集中，然后它们被配置为单实例。  有关可用性集的详细信息，请参阅[管理的 Windows 虚拟机的可用性](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)或[管理的 Linux 虚拟机的可用性](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

计划内的维护更新到单实例和多实例 Vm 单独发生。 通过重新配置你的 Vm 是单实例 （如果它们是多实例） 或将多实例 （如果它们是单实例），你可以控制当其虚拟机接收在计划内的维护。 请参阅[计划 Azure Linux 虚拟机的维护](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)或[计划 Azure Windows 虚拟机的维护](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)有关 Azure Vm 的计划内维护的详细信息。

## <a name="for-multi-instance-configuration"></a>多实例配置
你可以选择计划内的维护会影响你部署的 Vm 的可用性集的配置中通过从可用性集中删除这些虚拟机的时间。

1. 一封电子邮件发送到你为多实例配置中虚拟机计划内维护之前的七个日历日。 电子邮件的正文中包括的订阅 Id 和受影响的多实例 Vm 的名称。
2. 在这些七天内，你可以选择您的实例都将通过从其可用性集，在该区域中删除多实例 Vm 更新的时间。 在配置此更改会导致重新启动，在从一台物理主机，针对维护，以及不针对维护的另一台物理主机移动虚拟机。
3. 你可以从其可用性集在 Azure 门户中删除 VM。

   1. 在门户中，选择将 VM 从可用性集中删除。  

   2. 下**设置**，单击**可用性集**。

      ![可用性集选择](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. 在可用性集中下拉菜单中，选择"不属于可用性集。"

      ![从组中删除](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. 在顶部，单击**保存**。 单击**是**以确认此操作重启 VM。

   >[!TIP]
   >你可以通过重新配置到多实例 VM 更高版本中选择一个列出的可用性集。

4. 从可用性集中删除的虚拟机移到单实例主机和在可用性设置配置为在计划内维护期间不会更新。
5. 可用性集 Vm 的更新 （按照计划原始电子邮件中所述） 完成后，应将 Vm 添加回其可用性集。 成为可用性集的一部分重新 Vm 配置为多实例，并且会导致重新启动。 通常情况下，所有的多实例更新完成后在整个 Azure 环境，则遵循单实例维护。

从可用性集中删除 VM 还可以使用 Azure PowerShell:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>单实例配置
你可以选择计划内的维护会影响你的 Vm 中的单实例配置通过将这些虚拟机添加到可用性集的时间。

分步

1. 一封电子邮件发送给您到 Vm 中的单实例配置的计划内维护之前的七个日历日。 电子邮件的正文中包含的订阅 Id 和执行受影响的单实例虚拟机的名称。
2. 在这些七天内，你可以选择你的实例重新启动通过将单实例虚拟机添加到该同一区域中的可用性集的时间。 在配置此更改会导致重新启动，在从一台物理主机，针对维护，以及不针对维护的另一台物理主机移动虚拟机。
3. 按照说明此处将现有 Vm 添加到可用性集使用 Azure 门户和 Azure PowerShell。 （请参阅 Azure PowerShell 示例，请遵循以下步骤。）
4. 后为多实例重新配置这些虚拟机，它们不在计划内维护到单实例虚拟机。
5. 单实例虚拟机更新完成后 （根据在原始电子邮件的计划），你可以将 Vm 回到单实例，通过删除虚拟机从其可用性集。

将 VM 添加到可用性集，也可以使用 Azure PowerShell 来实现：

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
