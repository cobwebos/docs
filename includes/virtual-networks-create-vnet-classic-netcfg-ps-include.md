## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>如何创建虚拟网络使用网络配置文件从 PowerShell
Azure 使用 xml 文件来定义与订阅的所有可用的虚拟网络。 你可以下载此文件，编辑它以修改或删除现有的虚拟网络，并创建新的虚拟网络。 在本教程中，你将了解如何下载此文件中，称为网络配置 （或 netcfg） 文件，并编辑它以创建新的虚拟网络。 若要了解有关网络配置文件的详细信息，请参阅[Azure 虚拟网络配置架构](https://msdn.microsoft.com/library/azure/jj157100.aspx)。

若要使用 PowerShell netcfg 文件创建虚拟网络，请完成以下步骤：

1. 如果你从未使用过 Azure PowerShell，完成中的步骤[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)文章，然后登录到 Azure 并选择你的订阅。
2. 从 Azure PowerShell 控制台中，使用**Get-azurevnetconfig** cmdlet 来下载到你的计算机上的目录的网络配置文件，通过运行以下命令： 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   预期的输出：
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. 打开在步骤 2 使用任何 XML 或文本编辑器应用程序中保存的文件并查找 **<VirtualNetworkSites>** 元素。 如果你有已创建任何网络，每个网络显示为其自身 **<VirtualNetworkSite>** 元素。
4. 若要创建在此方案中所述的虚拟网络，请正下方添加以下 XML  **<VirtualNetworkSites>** 元素：

   ```xml
        <VirtualNetworkSite name="TestVNet" Location="East US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>
   ```
   
5. 保存网络配置文件。
6. 从 Azure PowerShell 控制台中，使用**Set-azurevnetconfig** cmdlet 上载网络配置文件，通过运行以下命令： 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   返回输出：
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   如果**OperationStatus**不*Succeeded*在返回的输出中，再次检查的 xml 文件错误并完成步骤 6。

7. 从 Azure PowerShell 控制台中，使用**Get-azurevnetsite** cmdlet 以验证新的网络已添加通过运行以下命令： 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   返回 （缩写） 的输出包括以下文本：
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
