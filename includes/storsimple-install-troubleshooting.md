<!--author=alkohli last changed: 03/17/16-->

## <a name="troubleshooting-update-failures"></a>排查更新失败
**出现升级前检查失败的通知时该怎么办？**

如果预先检查失败，请务必查看页面底部的详细通知栏。 其中提供了有关哪项预先检查失败的指导。 下图显示了会出现此类通知的某种场合。 在此情况下，控制器运行状况检查和硬件组件运行状况检查失败。 在“硬件状态”部分下，可以看到“控制器 0”和“控制器 1”组件需要引以关注。

  ![预先检查失败](./media/storsimple-install-troubleshooting/HCS_PreUpdateCheckFailed-include.png)

需要确保两个控制器状况良好且已联机。 此外，需要确保 StorSimple 设备中的所有硬件组件在“维护”页上都显示为状况良好。 然后，可以尝试安装更新。 如果无法修复硬件组件问题，请联系 Microsoft 支持人员了解后续措施。

**如果出现“无法安装更新”错误消息，并且系统建议参考更新故障排除指南确定失败原因，该怎么办？**

可能的原因之一是未连接到 Microsoft 更新服务器。 这需要执行手动检查。 如果与更新服务器断开连接，更新作业将会失败。 可以在 StorSimple 设备的 Windows PowerShell 界面中运行以下 cmdlet 来检查连接：

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

请在两个控制器上运行该 cmdlet。

如果确认连接未断开但持续出现此问题，请联系 Microsoft 支持人员了解后续措施。



<!--HONumber=Nov16_HO3-->


