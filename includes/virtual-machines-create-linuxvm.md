
1. 使用[从 Azure CLI 1.0 连接到 Azure](../articles/xplat-cli-connect.md) 中列出的步骤登录到 Azure 订阅。

2. 请确保你在经典部署模式下，如下所示：

    ```azurecli
    azure config mode asm
    ```

3. 从可用映像中找出要加载的 Linux 映像，如下所示：

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    在 Windows 命令提示符窗口中，使用 **find** 而不是 grep。
   
4. 通过 `azure vm create` 使用上一列表中的 Linux 映像创建 VM。 此步骤创建云服务和存储帐户。 还可通过 `-c` 选项将此 VM 连接到现有云服务。 通过 `-e` 选项创建 SSH 终结点以登录到 Linux 虚拟机。 以下示例使用 `West US` 位置中的 `Ubuntu-14_04_4-LTS` 映像创建名为 `myVM` 的 VM，并添加用户名 `ops`：
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    输出类似于以下示例：

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
   > 对于 Linux 虚拟机，必须在 `vm create` 中提供 `-e` 选项。 在创建该虚拟机后无法启用 SSH。 有关 SSH 的详细信息，请参阅[如何在 Azure 中将 SSH 用于 Linux](../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

5. 可以通过使用 `azure vm show` 命令来验证 VM 的属性。 以下示例列出名为 `myVM` 的 VM 的信息：

    ```azurecli   
    azure vm show myVM
    ```

6. 使用 `azure vm start` 命令启动 VM，如下所示：

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>后续步骤
有关所有这些 Azure CLI 1.0 虚拟机命令的详细信息，请参阅[将 Azure CLI 1.0 与经典部署 API 配合使用](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)。



<!--HONumber=Feb17_HO2-->


