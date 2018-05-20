Azure 现在提供对两种调试功能的支持：控制台输出和屏幕截图支持，适用于 Azure 虚拟机资源管理器部署模型。 

将自己的映像加载到 Azure 或者启动某个平台映像时，可能会因为许多原因而导致虚拟机进入无法启动状态。 有了这些功能，就可以轻松进行诊断，将虚拟机从启动故障恢复。

对于 Linux 虚拟机，可以轻松地在门户中查看控制台日志的输出：

![Azure 门户](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
但是，对于 Windows 和 Linux 虚拟机，Azure 也允许从虚拟机监控程序查看 VM 的屏幕截图：

![错误](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

所有区域的 Azure 虚拟机都支持这两项功能。 请注意，屏幕截图和输出可能需要长达 10 分钟的时间才能显示在存储帐户中。

## <a name="common-boot-errors"></a>常见的启动错误

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [找不到操作系统](https://support.microsoft.com/help/4010142)
- [启动故障或 INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>对新虚拟机启用诊断
1. 从 Azure 门户创建新的虚拟机时，请从部署模型下拉列表中选择“Azure 资源管理器”：
 
    ![资源管理器](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. 在“设置”中启用“启动诊断”，然后选择要在其中放置这些诊断文件的存储帐户。
 
    ![创建 VM](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > 启动诊断功能不支持高级存储帐户。 如果使用高级存储帐户进行启动诊断，则可能会在启动 VM 时收到 StorageAccountTypeNotSupported 错误。
    >
    > 

3. 若要从 Azure 资源管理器模板进行部署，请导航到虚拟机资源，并追加诊断配置文件部分。 记得使用“2015-06-15”API 版本标头。

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. 可以通过诊断配置文件选择要在其中放置这些日志的存储帐户。

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

若要部署启用了启动诊断功能的示例虚拟机，请在此处查看我们的存储库。

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>在现有的虚拟机上启用启动诊断 

若要在现有的虚拟机上启用启动诊断，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择虚拟机。
2. 在“支持 + 故障排除”中选择“启动诊断” > “设置”，将状态更改为“启用”，然后选择一个存储帐户。 
4. 确保选中“启动诊断”选项，然后保存所做的更改。

    ![更新现有 VM](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. 重新启动 VM，使设置生效。


