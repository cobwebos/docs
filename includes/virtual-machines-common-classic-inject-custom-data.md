


本主题介绍如何执行以下操作：

* 在预配 Azure 虚拟机时，将数据注入到 Azure 虚拟机 (VM) 中。
* 针对 Windows 和 Linux 检索它。
* 使用某些系统上提供的特殊工具来检测和自动处理自定义数据。

> [!NOTE]
> 本文介绍了如何才能使用通过 Azure 服务管理 API 创建的 VM 注入自定义数据。 若要了解如何使用 Azure Resource Management API，请参阅[示例模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata)。
> 
> 

## <a name="injecting-custom-data-into-your-azure-virtual-machine"></a>将自定义数据注入到 Azure 虚拟机中
当前仅在 [Azure 命令行接口](https://github.com/Azure/azure-xplat-cli)上支持此功能。 此处我们将要创建一个包含数据的 `custom-data.txt` 文件，然后在预配期间将它注入 VM。 尽管可以使用 `azure vm create` 命令的任何选项，但以下内容演示的是一个非常基本的方法：

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## <a name="using-custom-data-in-the-virtual-machine"></a>在虚拟机中使用自定义数据
* 如果 Azure VM 是基于 Windows 的 VM，则自定义数据文件将保存到 `%SYSTEMDRIVE%\AzureData\CustomData.bin`。 虽然它已进行 Base64 编码，以便从本地计算机传输到新 VM，但它会自动解码并可以立即打开或使用。
  
  > [!NOTE]
  > 如果该文件存在，它会被覆盖。 目录的安全性已设为 **System:Full Control** 和 **Administrators:Full Control**。
  > 
  > 
* 如果 Azure VM 是基于 Linux 的 VM，则自定义数据文件将位于下列其中一个位置，具体取决于分发版。 数据可能采用 base64 编码，因此可能需要先解码数据：
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## <a name="cloud-init-on-azure"></a>Azure 中的 Cloud-init
如果 Azure VM 来自 Ubuntu 或 CoreOS 映像，则可以使用 CustomData 将 cloud-config 发送到 cloud-init。 或者，如果自定义数据文件是一个脚本，则 cloud-init 只需执行它。

### <a name="ubuntu-cloud-images"></a>Ubuntu 云映像
在大多数 Azure Linux 映像中，将编辑“/etc/waagent.conf”来配置临时资源磁盘和交换文件。 有关详细信息，请参阅 [Azure Linux 代理用户指南](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

但是，在 Ubuntu 云映像上，必须使用 cloud-init 来配置资源磁盘（又称“临时”磁盘）和交换分区。 有关更多详细信息，请参阅 Ubuntu wiki 上的以下页面：[AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions)。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps-using-cloud-init"></a>后续步骤：使用 cloud-init
有关详细信息，请参阅[适用于 Ubuntu 的 cloud-init 文档](https://help.ubuntu.com/community/CloudInit)。

<!--Link references-->
[添加 Azure 角色服务管理 REST API 参考](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Azure 命令行接口](https://github.com/Azure/azure-xplat-cli)

