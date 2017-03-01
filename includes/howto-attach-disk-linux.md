
有关磁盘的详细信息，请参阅[关于虚拟机的磁盘和 VHD](../articles/storage/storage-about-disks-and-vhds-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>附加空磁盘
1. 打开 Azure CLI 1.0 并[连接到 Azure 订阅](../articles/xplat-cli-connect.md)。 确保是在 Azure 服务管理模式 (`azure config mode asm`) 下。
2. 输入 `azure vm disk attach-new` 以创建并附加新磁盘，如以下示例所示。 将 *myVM* 替换为 Linux 虚拟机的名称，并指定磁盘的大小（以 GB 为单位），在此示例中为 *100 GB*：

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. 创建并附加数据磁盘后，它列出在 `azure vm disk list <virtual-machine-name>` 的输出中，如以下示例所示：
   
    ```azurecli
    azure vm disk list TestVM
    ```

    输出类似于以下示例：

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>附加现有磁盘
附加现有磁盘需要存储帐户中具有可用的 .vhd。

1. 打开 Azure CLI 1.0 并[连接到 Azure 订阅](../articles/xplat-cli-connect.md)。 确保是在 Azure 服务管理模式 (`azure config mode asm`) 下。
2. 检查要附加的 VHD 是否已上载到 Azure 订阅：
   
    ```azurecli
    azure vm disk list
    ```

    输出类似于以下示例：

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. 如果未找到要使用的磁盘，可以使用 `azure vm disk create` 或 `azure vm disk upload` 将本地 VHD 上载到你的订阅。 `disk create` 的示例将如以下示例所示：
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    输出类似于以下示例：

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   也可以使用 `azure vm disk upload` 将 VHD 上载到特定的存储帐户。 详细阅读命令，以管理你[在这里](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)的 Azure 虚拟机数据磁盘。

4. 现在将所需的 VHD 附加到你的虚拟机：
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   请确保将 *myVM* 替换为虚拟机的名称，并将 *myVHD* 替换为所需的 VHD。

5. 可以使用 `azure vm disk list <virtual-machine-name>` 验证磁盘是否已附加到虚拟机：
   
    ```azurecli
    azure vm disk list myVM
    ```

    输出类似于以下示例：

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> 在添加数据磁盘后，需要登录到虚拟机并初始化磁盘，然后虚拟机才能使用磁盘来存储数据（有关如何初始化磁盘的详细信息，请参阅以下步骤）。
> 
> 

