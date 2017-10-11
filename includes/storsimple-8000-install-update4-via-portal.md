<!--author=alkohli last changed: 07/07/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>从 Azure 门户安装更新

1. 在“StorSimple 服务”页上，选择设备。

    ![选择设备](./media/storsimple-8000-install-update4-via-portal/update1.png)

2. 导航到“设备设置” > “设备更新”。

    ![单击“设备更新”](./media/storsimple-8000-install-update4-via-portal/update2.png)

2. 如果有新的更新可用，则会显示一条通知。 或者，在“设备更新”边栏选项卡中，单击“扫描更新”。 已创建一个用于扫描可用更新的作业。 在作业成功完成时，系统会发出通知。

    ![单击“设备更新”](./media/storsimple-8000-install-update4-via-portal/update3.png)

3. 我们建议在你的设备上应用更新之前查看发行说明。 若要应用更新，请单击“安装更新”。 在“确认常规更新”边栏选项卡中，在应用更新之前，复查要完成的先决条件。 选中指示已做好更新准备的复选框，然后单击“安装”。

    ![单击“设备更新”](./media/storsimple-8000-install-update4-via-portal/update4.png)

6. 此时会开始执行一系列先决条件检查。 这些检查包括：
   
   * **控制器健康状况检查**：验证两个设备控制器是否状况良好且联机。
   * **硬件组件运行状况检查**：验证 StorSimple 设备上的所有硬件组件是否状况良好。
   * **DATA 0 检查**：验证设备上是否已启用 DATA 0。 如果未启用此接口，必须将它启用，并重试。

    只有所有检查都成功完成后才会下载并安装更新。 检查正在进行时，会通知您。 如果预检查失败，系统会提供失败原因。 请解决这些问题，然后重试操作。 如果无法自行解决这些问题，可能需要联系 Microsoft 支持人员。

7. 预检查成功完成后，将创建更新作业。 成功创建更新作业后，系统会发出通知。
   
    ![创建更新作业](./media/storsimple-8000-install-update4-via-portal/update6.png)
   
    随后会在你的设备上应用更新。

9. 更新需要数小时才能完成。 随时可以选择更新作业并单击“详细信息”来查看作业详细信息。

    ![创建更新作业](./media/storsimple-8000-install-update4-via-portal/update8.png)

     还可以从“设备设置”>“作业”检索更新作业的进度。 在“作业”边栏选项卡上，可以看到更新进度。

     ![创建更新作业](./media/storsimple-8000-install-update4-via-portal/update7.png)

10. 在作业完成后，导航到“设备设置”>“设备更新”。 软件版本现在应当已更新。

    ![创建更新作业](./media/storsimple-8000-install-update4-via-portal/update9.png)

