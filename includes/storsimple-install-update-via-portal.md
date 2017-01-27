<!--author=SharS last changed: 01/15/2016-->

#### <a name="to-install-update-12-from-the-azure-classic-portal"></a>通过 Azure 经典门户安装 Update 1.2
1. 在“StorSimple 服务”页上，选择你的设备。 导航到“设备” > “维护”。
2. 在页面底部，单击“扫描更新”。 此时将创建一个扫描可用更新的作业。 作业成功完成时，系统会发出通知。
3. 在同一页上的“软件更新”部分中，可以看到有新的软件更新可用。 我们建议先查看版本信息，然后在设备上应用 Update 1.2。
   
    ![安装软件更新](./media/storsimple-install-update-via-portal/InstallUpdate12_11M.png)
4. 在页面底部，单击“安装更新”。
5. 系统将提示进行确认。 单击 **“确定”**。
6. 此时将显示“安装更新”对话框。 设备应符合此对话框中列出的检查项。 这些检查步骤是在更新之前完成的。 选择“我了解上述要求，已准备好更新设备”。 单击选中图标。
   
    ![确认消息](./media/storsimple-install-update-via-portal/InstallUpdate12_2M.png)
7. 接下来，将开始执行一系列自动预先检查。 其中包括：
   
   * **控制器健康状况检查**：验证两个设备控制器是否状况良好且联机。
   * **硬件组件运行状况检查**：验证 StorSimple 设备上的所有硬件组件是否状况良好。
   * **DATA 0 检查**：验证设备上是否已启用 DATA 0。 如果未启用此接口，需要将它启用，然后重试。
   * **DATA 2 和 DATA 3 检查**：验证 DATA 2 和 DATA 3 网络接口是否未启用。 如果已启用这些接口，需要将其禁用，然后再尝试更新设备。 仅当要从运行 GA 软件的设备更新时，才需要执行此项检查。 运行 0.1、0.2 或 0.3 版本的设备不需要此项检查。
   * **网关检查**：在运行 Update 1 之前版本的任何设备上执行。 此项检查只能在运行 Update 1 之前版本软件的所有设备上运行。在为除 DATA 0 以外的网络接口配置了网关的设备上，此项检查将会失败。
     
     仅当上述所有更新前检查都已成功完成之后，才应用 Update 1.2。 系统会告知更新前检查正在进行。
     
     ![预先检查通知](./media/storsimple-install-update-via-portal/InstallUpdate12_3M.png)
     
     下面是升级前检查失败的示例。 需要验证两个设备控制器是否状况良好且联机。 还需要检查硬件组件的运行状况。 在此示例中，控制器 0 和控制器 1 组件需要引以关注。 如果无法自行解决这些问题，可能需要联系 Microsoft 支持人员。
     
       ![预先检查失败](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)
     
     > [!NOTE]
     > 在 StorSimple 设备上应用 Update 1.2 之后，将来的更新不再需要执行 DATA 2 和 DATA 3 检查以及网关检查。 但仍需执行其他预先检查。
     > 
     > 
8. 升级前检查成功完成后，将创建更新作业。 成功创建更新作业时，系统会发出通知。
   
    ![创建更新作业](./media/storsimple-install-update-via-portal/InstallUpdate12_44M.png)
   
    然后，将在设备上应用更新。
9. 若要监视更新作业的进度，请单击“查看作业”。 在“作业”页上可以看到更新进度。 
   
    ![更新作业进度](./media/storsimple-install-update-via-portal/InstallUpdate12_5M.png)
10. 更新需要几个小时才能完成。 随时可以查看作业的详细信息。
    
    ![更新作业详细信息](./media/storsimple-install-update-via-portal/InstallUpdate12_6M.png)
11. 作业完成后，请导航到“维护”页，然后向下滚动到“软件更新”。
12. 确保设备运行的是 **StorSimple 8000 系列 Update 1.2 (6.3.9600.17584)**。 还应修改“上次更新日期”。
    
    ![“维护”页](./media/storsimple-install-update-via-portal/InstallUpdate12_10M.png)
13. 现在，系统会显示有维护模式的更新可用。 这些更新为中断性更新，会导致设备停机，只能通过设备的 Windows PowerShell 接口应用。 请遵循[安装维护模式更新](../articles/storsimple/storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)中的说明，通过适用于 StorSimple 的 Windows PowerShell 安装这些更新。

> [!NOTE]
> 在某些情况下，在设备上成功应用维护模式更新后，指出有维护模式更新可用的消息可能会持续显示长达 24 小时。  
> 
> 



<!--HONumber=Jan17_HO3-->


