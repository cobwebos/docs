
1. 登录到 Azure 订阅使用中列出的步骤[从 Azure CLI 1.0 连接到 Azure](../articles/xplat-cli-connect.md)。

2. 请确保你在经典部署模式下，如下所示：

    ```azurecli
    azure config mode asm
    ```

3. 找出你想要从可用映像，如下所示加载的 Linux 映像：

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    在 Windows 命令提示符窗口中，使用**查找**而不是 grep。
   
4. 使用`azure vm create`从前面的列表的 Linux 映像创建 VM。 此步骤将创建的云服务和存储帐户。 你也可以连接到现有云服务与此 VM`-c`选项。 创建 SSH 终结点以登录到 Linux 虚拟机与`-e`选项。 下面的示例创建名为的 VM`myVM`使用`Ubuntu-14_04_4-LTS`中`West US`位置，并将添加的用户名称`ops`:
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    输出为类似于下面的示例：

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > 对于 Linux 虚拟机，你必须提供`-e`选项`vm create`。 不能在创建虚拟机后启用 SSH。 SSH 的详细信息，请参阅[如何使用 SSH 在 Azure 上通过 Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

5. 你可以通过使用验证 VM 的属性`azure vm show`命令。 下面的示例列出名为的 VM 的信息`myVM`:

    ```azurecli   
    azure vm show myVM
    ```

6. 启动你的 VM 与`azure vm start`命令，如下所示：

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>后续步骤
所有这些 Azure CLI 1.0 虚拟机命令的信息，请参阅[与经典部署 API 结合使用 Azure CLI 1.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)。

