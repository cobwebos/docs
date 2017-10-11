



根据你的环境和选择，该脚本可以创建所有群集基础结构，包括 Azure 虚拟网络、 存储帐户、 云服务、 域控制器、 远程或本地 SQL 数据库、 头节点和其他群集节点。 或者，该脚本可以使用预先存在的 Azure 基础结构，并创建仅存在于 HPC 群集节点。

有关规划 HPC Pack 群集的背景信息，请参阅[产品评估和规划](https://technet.microsoft.com/library/jj899596.aspx)和[入门](https://technet.microsoft.com/library/jj899590.aspx)HPC Pack 2012 R2 的 TechNet 库中的内容。

## <a name="prerequisites"></a>必备条件
* **Azure 订阅**： 你可以使用 Azure 全球或 Azure 中国服务中的订阅。 你可以部署的群集节点的类型和数量，会影响你的订阅限制。 有关信息，请参阅[Azure 订阅和服务限制、 配额和约束](../articles/azure-subscription-service-limits.md)。
* **Windows 客户端计算机使用 Azure PowerShell 0.8.10 或更高版本安装和配置**： 请参阅[要开始使用 Azure PowerShell](/powershell/azureps-cmdlets-docs)有关安装说明和步骤，连接到你的 Azure 订阅。
* **HPC Pack IaaS 部署脚本**： 下载并解压缩最新版本中的脚本[Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949)。 检查脚本的版本，通过运行`New-HPCIaaSCluster.ps1 –Version`。 本文基于版本 4.5.2 的脚本。
* **脚本配置文件**： 创建脚本用来配置 HPC 群集的 XML 文件。 有关更多信息和示例，请参阅本文和随附的部署脚本的 Manual.rtf 文件中更高版本的部分。

## <a name="syntax"></a>语法
```PowerShell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> 以管理员身份运行脚本。
> 
> 

### <a name="parameters"></a>参数
* **ConfigFile**： 指定描述 HPC 群集的配置文件的文件路径。 请参阅有关本主题中的配置文件的详细信息，或包含该脚本中的文件夹中的 Manual.rtf 文件。
* **AdminUserName**： 指定的用户名。 如果域林由脚本创建的这将成为所有 Vm 的本地管理员用户名和域管理员名称。 如果域林已存在，此参数指定的域用户要安装 HPC Pack 的本地管理员用户名。
* **AdminPassword**： 指定管理员的密码。 如果未指定命令行中，脚本将提示你输入密码。
* **HPCImageName** （可选）： 指定用于部署 HPC 群集的 HPC Pack VM 映像名称。 它必须是从 Azure 应用商店提供 Microsoft HPC Pack 映像。 如果未指定 （通常推荐的），脚本将选择最新发布[HPC Pack 2012 R2 映像](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)。 最新映像基于 HPC Pack 2012 R2 Update 3 安装带有 Windows Server 2012 R2 Datacenter。
  
  > [!NOTE]
  > 如果未指定有效的 HPC Pack 映像，则部署将失败。
  > 
  > 
* **日志文件**（可选）： 指定部署日志文件路径。 如果未指定，脚本将创建一个日志文件运行脚本的计算机的 temp 目录中。
* **强制**（可选）： 抑制所有确认提示。
* **NoCleanOnFailure** （可选）： 指定未成功部署在 Azure 虚拟机不会删除。 重新运行脚本以继续部署之前手动删除这些虚拟机或部署可能失败。
* **PSSessionSkipCACheck** （可选）： 此脚本所部署的 Vm 与每个云服务，自签名的证书自动由 Azure 生成，和云服务中的所有 Vm 都使用此证书作为默认 Windows 远程管理 (WinRM) 证书。 若要部署在这些 Azure Vm 中的 HPC 功能，脚本将按默认中暂时安装这些证书在本地计算机\\要抑制执行脚本期间发生的"不受信任的 CA"安全错误的客户端计算机的受信任的根证书颁发机构存储区。 该脚本完成后删除证书。 如果指定此参数，则在客户端计算机中，将不安装证书，并且会抑制安全警告。
  
  > [!IMPORTANT]
  > 有关生产部署不建议指定此参数。
  > 
  > 

### <a name="example"></a>示例
下面的示例创建使用配置文件的 HPC Pack 群集*MyConfigFile.xml*，并指定用于安装该群集的管理员凭据。

```PowerShell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>其他注意事项
* 脚本 （可选） 可以启用通过 HPC Pack web 门户或 HPC Pack REST API 提交作业。
* 如果你想要安装其他软件或配置其他设置，该脚本 （可选） 可以在头节点上运行自定义前和配置后脚本。

## <a name="configuration-file"></a>配置文件
部署脚本的配置文件是 XML 文件。 架构文件 HPCIaaSClusterConfig.xsd 位于 HPC Pack IaaS 部署脚本文件夹。 **IaaSClusterConfig**是配置文件，其中包含部署脚本文件夹中的 Manual.rtf 文件详细信息中所述的子元素的根元素。

