## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>如何创建经典 VNet 使用 Azure CLI
可以使用 Azure CLI 从运行 Windows、 Linux 或 OSX 的任何计算机从命令提示符管理 Azure 资源。 若要使用 Azure CLI 创建 VNet，请执行以下步骤。

1. 如果你从未使用过 Azure CLI，请参阅[安装和配置 Azure CLI](../articles/cli-install-nodejs.md)并按照说明进行操作，直到选择 Azure 帐户和订阅。
2. 运行**azure 网络 vnet 创建**命令以创建 VNet 和子网，如下所示。 在输出后显示的列表说明了使用这些参数。
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    预期的输出：
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. 要创建的 VNet 的名称。 对于我们的方案， *TestVNet*
   * **-e （或---address-space）**。 VNet 地址空间。 对于我们的方案， *192.168.0.0*
   * **-i （或-cidr）**。 以 CIDR 格式的网络掩码。 对于我们的方案， *16*。
   * **-n (或---subnet-name**)。 第一个子网的名称。 对于我们的方案，*前端*。
   * **-p （或--subnet-start-ip）**。 子网或子网地址空间起始 IP 地址。 对于我们的方案， *192.168.1.0*。
   * **-r （或---subnet-cidr）**。 子网的 CIDR 格式的网络掩码。 对于我们的方案， *24*。
   * **-l （或--位置）**。 将在其中创建 VNet 的 azure 区域。 对于我们的方案，*美国中南部*。
3. 运行**azure vnet 子网创建**命令以创建子网，如下所示。 在输出后显示的列表说明了使用这些参数。
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    下面是上述命令的预期的输出：
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (或---vnet-name**。 将在其中创建子网的 VNet 的名称。 对于我们的方案， *TestVNet*。
   * **-n （或--名称）**。 新的子网的名称。 对于我们的方案，*后端*。
   * **-a （或---address-prefix）**。 子网 CIDR 块。 四个我们的方案， *192.168.2.0/24*。
4. 运行**azure 网络 vnet 显示**命令以查看新 vnet 的属性，如下所示。
   
            azure network vnet show
   
    下面是上述命令的预期的输出：
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

