
Azure 现在提供对两种调试功能的支持：控制台输出和屏幕截图支持，适用于 Azure 虚拟机 Resource Manager 部署模型。 

将自己的映像加载到 Azure 或者启动某个平台映像时，可能会因为许多原因而导致虚拟机进入无法启动状态。 有了这些功能，就可以轻松进行诊断，将虚拟机从启动故障恢复。

对于 Linux 虚拟机，可以轻松地在门户中查看控制台日志的输出：

![Azure 门户](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
但是，对于 Windows 和 Linux 虚拟机，Azure 也允许从虚拟机监控程序查看 VM 的屏幕截图：

![错误](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

所有区域的 Azure 虚拟机都支持这两项功能。 请注意，屏幕截图和输出可能需要长达 10 分钟的时间才能显示在存储帐户中。

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>对新虚拟机启用诊断
1. 从预览门户创建新的虚拟机时，请从部署模型下拉列表中选择“Azure Resource Manager”：****
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. 配置“监视”选项，选择要在其中放置这些诊断文件的存储帐户。
 
    ![创建 VM](./media/virtual-machines-common-boot-diagnostics/screenshot4.jpg)

3. 若要从 Azure Resource Manager 模板进行部署，请导航到虚拟机资源，然后追加诊断配置文件部分。 记得使用“2015-06-15”API 版本标头。

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

## <a name="update-an-existing-virtual-machine"></a>更新现有虚拟机 ##

为了通过门户启用启动诊断功能，还可以通过门户更新现有虚拟机。 选择“启动诊断”选项，然后选择“保存”。 重新启动 VM，使设置生效。

![更新现有 VM](./media/virtual-machines-common-boot-diagnostics/screenshot5.png)

