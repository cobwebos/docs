### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>通过用于 StorSimple 的 Windows PowerShell 安装维护模式更新

将维护模式更新应用到 StorSimple 设备时，所有 I/O 请求都将暂停。 非易失性随机存取内存 (NVRAM) 或群集服务之类的服务也会停止。 在用户进入或退出此模式时，两个控制器都会重新启动。 退出此模式时，所有服务将继续运行并且可以正常运行。 （这可能需要几分钟的时间。）

> [!IMPORTANT]
> * 在进入维护模式前，验证两个设备控制器是否在 Azure 门户中正常运行。 如果控制器未正常运行，请[联系 Microsoft 支持部门](../articles/storsimple/storsimple-8000-contact-microsoft-support.md)，以了解后续步骤。
> * 处于维护模式下时，需要依次对两个控制器进行更新。

1. 使用 PuTTY 连接到串行控制台。 遵循 [Use PuTTy to connect to the serial console](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)（使用 PuTTy 连接到串行控制台）中的详细说明。 在命令提示符下，按 **Enter**。 选择“选项 1”，以完全访问权限登录到设备。

2. 若要将控制器置于维护模式，请键入：
    
    `Enter-HcsMaintenanceMode`

    两个控制器将重新启动并进入维护模式。

3. 安装维护模式更新。 键入：

    `Start-HcsUpdate`

    系统会提示进行确认。 确认更新后，它们将安装在当前正在访问的控制器上。 安装这些更新后，将重新启动控制器。

4. 监视更新的状态。 由于当前控制器正在更新，请登录到对等控制器，且无法处理任何其他命令。 键入：

    `Get-HcsUpdateStatus`

    如果 `RunInProgress` 是 `True`，表示更新仍在进行中。 如果 `RunInProgress` 是 `False`，表示更新已完成。

5. 在成功应用磁盘固件更新并重新启动更新的控制器后，验证磁盘固件版本。 在已更新的控制器上，键入：

    `Get-HcsFirmwareVersion`
   
    预期磁盘固件版本为：`XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. 退出维护模式。 针对每个设备控制器键入以下命令：

    `Exit-HcsMaintenanceMode`

    退出维护模式时，控制器会重新启动。

7. 返回到 Azure 门户。 门户在 24 小时内可能不会显示已安装维护模式更新。