---
title: "用于 Excel 和 SOA 的 HPC Pack 群集 | Microsoft Docs"
description: "开始在 Azure 的 HPC Pack 群集上运行大型 Excel 和 SOA 工作负荷"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: acd2ee7fb94c43493ffd9ffee157f2c3e795b63e
ms.contentlocale: zh-cn
ms.lasthandoff: 06/03/2017

---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>开始在 Azure 的 HPC Pack 群集上运行 Excel 和 SOA 工作负荷
本文介绍如何使用 Azure 快速入门模板或 Azure PowerShell 部署脚本将 Microsoft HPC Pack 2012 R2 群集部署到 Azure 虚拟机。 此群集使用 Azure 应用商店 VM 映像，这些映像根据设计可以通过 HPC Pack 运行 Microsoft Excel 工作负荷或面向服务的体系结构 (SOA) 工作负荷。 可使用群集从本地客户端计算机运行 Excel HPC 和 SOA 服务。 Excel HPC 服务提供 Excel 工作簿卸载和 Excel 用户定义的函数或 UDF。

> [!IMPORTANT] 
> 本文以 HPC Pack 2012 R2 的功能、模板和脚本为基础。 HPC Pack 2016 当前不支持此方案。
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

下图概略性地显示了用户创建的 HPC Pack 群集。

![具有运行 Excel 工作负荷的节点的 HPC 群集][scenario]

## <a name="prerequisites"></a>先决条件
* **客户端计算机** - 需要一台基于 Windows 的客户端计算机，向群集提交示例性的 Excel 和 SOA 作业。 此外还需一台运行 Azure PowerShell 群集部署脚本（如果选择该部署方法）的 Windows 计算机。
* **Azure 订阅** - 如果没有 Azure 订阅，只需几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* **核心配额** - 可能需要增加核心配额，尤其是在部署多个多核 VM 大小的群集节点时。 如果使用的是 Azure 快速入门模板，Resource Manager 中的核心配额基于 Azure 区域。 这种情况下，可能需要增加特定区域的配额。 请参阅 [Azure 订阅限制、配额和约束](../../azure-subscription-service-limits.md)。 若要增加配额，可免费[建立联机客户支持请求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。
* **Microsoft Office 许可证** - 如果使用包含 Microsoft Excel 的 Marketplace HPC Pack 2012 R2 VM 映像部署计算节点，将安装 Microsoft Excel 专业增强版 2013 的 30 天评估版。 评估期以后，需要提供有效的 Microsoft Office 许可证来激活 Excel，才能继续运行工作负荷。 请参阅本文后面部分的 [Excel 激活](#excel-activation)。 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>步骤 1。 在 Azure 中设置 HPC Pack 群集
本文介绍两种设置 HPC Pack 2012 R2 群集的选项：第一种方法使用 Azure 快速入门模板和 Azure 门户；第二种方法使用 Azure PowerShell 部署脚本。

### <a name="option-1-use-a-quickstart-template"></a>选项 1. 使用快速入门模板
使用 Azure 快速入门模板可在 Azure 门户中快速部署 HPC Pack 群集。 在门户中打开该模板时，会显示简单的 UI，可以在其中输入群集的设置。 下面是相关步骤。 

> [!TIP]
> 如果需要，可以使用 [Azure 应用商店模板](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn)专门为 Excel 工作负荷创建类似的群集。 步骤与下文中的内容稍有不同。
> 
> 

1. 访问 [GitHub 上的“创建 HPC 群集”模板页](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)。 如果需要，查看有关该模板和源代码的信息。
2. 单击“部署到 Azure”以启动使用 Azure 门户中的模板的部署。
   
   ![将模板部署到 Azure][github]
3. 在门户中，按照以下步骤输入 HPC 群集模板的参数。
   
   a. 在“参数”页上，输入或修改模板参数的值。 （单击每个设置旁边的图标可获得帮助信息。）下面的屏幕中会显示示例值。 本示例会在 *hpc.local* 域中创建名为 *hpc01* 的群集，该群集由 1 个头节点和 2 个计算节点组成。 将从包含 Microsoft Excel 的 HPC Pack VM 映像创建计算节点。
   
   ![输入参数][parameters-new-portal]
   
   > [!NOTE]
   > 将在 Windows Server 2012 R2 上从 HPC Pack 2012 R2 的[最新应用商店映像](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)自动创建头节点 VM。 当前，该映像基于 HPC Pack 2012 R2 Update 3。
   > 
   > 将从所选计算节点系列的最新映像创建计算节点 VM。 为包含 Microsoft Excel Professional Plus 2013 的评估版本的最新 HPC Pack 计算节点映像选择 **ComputeNodeWithExcel** 选项。 若要为常规 SOA 会话或 Excel UDF 卸载部署群集，请选择 **ComputeNode** 选项（不会安装 Excel）。
   > 
   > 
   
   b. 选择订阅。
   
   c. 为群集创建资源组，如 *hpc01RG*。
   
   d.单击“下一步”。 选择资源组的位置，例如“美国中部”。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 在“法律条款”页上，查看条款。 如果同意条款，请单击“购买”。 在完成为模板设置值后，单击“创建”。
4. 在部署完成时（通常需要花费大约 30 分钟），从群集头节点导出群集证书文件。 在稍后的步骤中，会在客户端计算机上导入此公用证书，为安全 HTTP 绑定提供服务器端身份验证。
   
   a. 在 Azure 门户中，通过远程桌面转到仪表板，选择头节点，然后在页面顶部单击“连接”以进行连接。
   
    <!-- ![Connect to the head node][connect] -->
   
   b. 在证书管理器中使用标准过程导出不带私钥的头节点证书（位于 Cert:\LocalMachine\My 下）。 在此示例中，导出 *CN = hpc01.eastus.cloudapp.azure.com*。
   
   ![导出证书][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>选项 2. 使用 HPC Pack IaaS 部署脚本
HPC Pack IaaS 部署脚本提供了另一种通用的方法来部署 HPC Pack 群集。 它会在经典部署模型中创建一个群集，该模板使用 Azure Resource Manager 部署模型。 此外，该脚本与 Azure 全球或 Azure 中国服务中的订阅兼容。

**其他先决条件**

* **Azure PowerShell** - [ - 在客户端计算机上安装并配置 Azure PowerShell](/powershell/azure/overview)（版本 0.8.10 或更高版本）。
* **HPC Pack IaaS 部署脚本** - 从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=44949)下载并解压缩最新版本的脚本。 通过运行 `New-HPCIaaSCluster.ps1 –Version` 检查脚本的版本。 本文基于版本 4.5.0 或更高版本的脚本。

**创建配置文件**

 HPC Pack IaaS 部署脚本使用描述 HPC 群集基础结构的 XML 配置文件作为输入。 要部署由 1 个头节点和 18 个计算节点（从包含 Microsoft Excel 的计算节点映像创建）组成的群集，请你将环境的值代入下面的示例配置文件。 有关配置文件的详细信息，请参阅脚本文件夹中的 Manual.rtf 文件和[使用 HPC Pack IaaS 部署脚本创建 HPC 群集](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**有关配置文件的说明**

* 头节点的 **VMName** **必须**与 **ServiceName** 相同，否则 SOA 作业无法运行。
* 请确保指定 **EnableWebPortal**，以便生成头节点证书并将其导出。
* 该文件指定配置后 PowerShell 脚本 PostConfig.ps1，可在头节点上运行。 以下示例脚本可配置 Azure 存储连接字符串、从头节点删除计算节点角色，以及在部署所有节点后将这些节点联机。 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**运行脚本**

1. 在客户端计算机上以管理员身份打开 PowerShell 控制台。
2. 将目录更改到脚本文件夹（在此示例中为 E:\IaaSClusterScript）。
   
   ```
   cd E:\IaaSClusterScript
   ```
3. 若要部署 HPC Pack 群集，请运行以下命令。 本示例假定配置文件位于 E:\HPCDemoConfig.xml。
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

HPC Pack 部署脚本可运行一段时间。 此脚本的一项功能是导出并下载群集证书，然后将其保存到客户端计算机上当前用户的 Documents 文件夹中。 此脚本生成如下消息。 在下面的步骤中，需在相应的证书存储中导入证书。    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>步骤 2. 卸载 Excel 工作簿并从本地客户端运行 UDF
### <a name="excel-activation"></a>Excel 激活
当对生产工作负荷使用 ComputeNodeWithExcel VM 映像时，需要提供有效的 Microsoft Office 许可密钥才能在计算节点上激活 Excel。 否则，Excel 评估版会在 30 天后过期，运行 Excel 工作簿会失败并显示 COMException (0x800AC472)。 

可通过以下操作让 Excel 再次获得 30 天的评估期：登录到头节点并通过 HPC 群集管理器 clusrun 所有 Excel 计算节点上的 `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe`。 最多可以重置两次。 然后，必须提供有效的 Office 许可密钥。

安装在 VM 映像上的 Office 专业增强版 2013 是使用通用批量许可密钥 (GVLK) 的批量版。 可以通过密钥管理服务 (KMS)/基于 Active Directory 的激活 (AD-BA) 或多次激活密钥 (MAK) 激活它。 

    * 若要使用 KMS/AD-BA，请使用现有的 KMS 服务器，或使用 Microsoft Office 2013 批量许可包设置新服务器。 （如果需要，可在头节点上设置服务器。）然后，通过 Internet 或电话激活 KMS 主机密钥。 然后运行 clusrun `ospp.vbs` 设置 KMS 服务器和端口，并激活所有 Excel 计算节点上的 Office。 

    * 要使用 MAK，请先运行 clusrun `ospp.vbs` 输入密钥，然后通过 Internet 或电话激活所有 Excel 计算节点。 

> [!NOTE]
> Office 专业增强版 2013 的零售产品密钥不适用于此 VM 映像。 如果拥有不属此 Office 专业增强版 2013 批量版的 Office 或 Excel 版本的有效密钥和安装媒体，则也可使用这些版本。 请首先卸载此批量版，然后安装所拥有的版本。 可捕获重新安装的 Excel 计算节点作为自定义 VM 映像，以便在大规模部署中使用。
> 
> 

### <a name="offload-excel-workbooks"></a>卸载 Excel 工作簿
按照下列步骤卸载 Excel 工作簿，使之能够在 Azure 中的 HPC Pack 群集上运行。 为此，必须已在客户端计算机上安装 Excel 2010 或 Excel 2013。

1. 使用步骤 1 中的选项之一通过 Excel 计算节点映像部署 HPC Pack 群集。 获取群集证书文件 (.cer) 和群集用户名和密码。
2. 在客户端计算机上导入 Cert:\CurrentUser\Root 下的群集证书。
3. 确保已安装 Excel。 使用与客户端计算机上的 Excel.exe 位于同一文件夹中的以下内容创建 Excel.exe.config 文件。 此步骤可确保 HPC Pack 2012 R2 Excel COM 外接程序成功加载。
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. 设置客户端，以便将作业提交到 HPC Pack 群集。 一个选项是下载完整的 [HPC Pack 2012 R2 Update 3 安装](http://www.microsoft.com/download/details.aspx?id=49922)，并安装 HPC Pack 客户端。 也可为计算机下载并安装 [HPC Pack 2012 R2 Update 3 客户端实用工具](https://www.microsoft.com/download/details.aspx?id=49923)和相应的 Visual C++ 2010 可再发行组件（[x64](http://www.microsoft.com/download/details.aspx?id=14632)、[x86](https://www.microsoft.com/download/details.aspx?id=5555)）。
5. 此示例使用名为 ConvertiblePricing_Complete.xlsb 的示例 Excel 工作簿。 可以在[此处](https://www.microsoft.com/en-us/download/details.aspx?id=2939)下载。
6. 将 Excel 工作簿复制到工作文件夹，例如 D:\Excel\Run。
7. 打开 Excel 工作簿。 在“开发”功能区上，单击“COM 外接程序”并确认 HPC Pack Excel COM 外接程序已成功加载。
   
   ![HPC Pack 的 Excel 外接程序][addin]
8. 通过更改注释行编辑 Excel 中的 VBA 宏 HPCControlMacros，如下面的脚本中所示。 替换为环境的相应值。
   
   ![HPC Pack 的 Excel 宏][macro]
   
   ```
   'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
   Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"
   
   'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
   Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.Initialize ActiveWorkbook
   HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles
   
   'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
   HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
   HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
   ```
9. 将 Excel 工作簿复制到某个上传目录，例如 D:\Excel\Upload。 此目录在 VBA 宏的 HPC_DependsFiles 常量中指定。
10. 若要在 Azure 中的群集上运行该工作簿，请单击工作表上的“群集”按钮。

### <a name="run-excel-udfs"></a>运行 Excel UDF
若要运行 Excel UDF，请按照前面的步骤 1 – 3 设置客户端计算机。 对于 Excel UDF，不需在计算节点上安装 Excel 应用程序。 因此，在创建群集计算节点时，可以选择常规计算节点映像而非使用 Excel 的计算节点映像。

> [!NOTE]
> 在 Excel 2010 和 Excel 2013 群集连接器对话框中有 34 字符限制。 可以使用此对话框指定运行 UDF 的群集。 如果完整的群集名称较长（例如 hpcexcelhn01.southeastasia.cloudapp.azure.com），则它会在对话框中容纳不下。 解决方法是设置一个计算机范围的变量（例如 *CCP_IAASHN*），其值为群集的长名称。 然后，在对话框中输入 *%CCP_IAASHN%* 作为群集头节点名称。 
> 
> 

成功部署群集后，继续使用以下步骤来运行示例内置 Excel UDF。 对于自定义 Excel UDF，请参阅这些[资源](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx)来构建 XLL并将它们部署在 IaaS 群集上。

1. 打开一个新的 Excel 工作簿。 在“开发”功能区上，单击“外接程序”。然后，在对话框中单击“浏览”，导航到 %CCP_HOME%Bin\XLL32 文件夹，并选择示例 ClusterUDF32.xll。 如果 ClusterUDF32 未存在于客户端计算机上，请从头节点上的 %CCP_HOME%Bin\XLL32 文件夹复制它。
   
   ![选择 UDF][udf]
2. 单击“文件” > “选项” > “高级”。 在“公式”下，选中“允许用户定义的 XLL 函数运行计算群集”。 然后，单击“选项”，在“群集头节点名称”中输入完整的群集名称。 （如前所述，此输入框限制为 34 个字符，因此较长的群集名称可能容纳不下。 可以在此处使用计算机范围的变量作为群集的长名称。）
   
   ![配置 UDF][options]
3. 要在群集上运行 UDF 计算，请单击值为 XllGetComputerNameC() 的单元格，并按 Enter。 该函数只检索运行 UDF 的计算节点的名称。 第一次运行时，凭据对话框会提示输入用于连接到 IaaS 群集的用户名和密码。
   
   ![运行 UDF][run]
   
   当有许多单元格要计算时，按 Alt-Shift-Ctrl + F9 可运行所有单元格上的计算。

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>步骤 3. 从本地客户端运行 SOA 工作负荷
若要在 HPC Pack IaaS 群集上运行常规 SOA 应用程序，首先请使用步骤 1 中的方法之一部署该群集。 在此示例中，请指定一个泛型计算节点映像，因为计算节点上不需 Excel。 然后，执行以下步骤。

1. 检索群集证书之后，在客户端计算机上的 Cert: \CurrentUser\Root 下导入它。
2. 安装 [HPC Pack 2012 R2 Update 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) 和 [HPC Pack 2012 R2 Update 3 客户端实用工具](https://www.microsoft.com/download/details.aspx?id=49923)。 可以使用这些工具开发和运行 SOA 客户端应用程序。
3. 下载 HelloWorldR2 [示例代码](https://www.microsoft.com/download/details.aspx?id=41633)。 在 Visual Studio 2010 或 2012 中打开 HelloWorldR2.sln。 （此示例当前与较新版本的 Visual Studio 不兼容。）
4. 先生成 EchoService 项目。 然后，按照部署到本地群集的相同方式将服务部署到 IaaS 群集。 有关详细步骤，请参阅 HelloWordR2 中的 Readme.doc。 按以下部分所述修改并生成 HelloWorldR2 及其他项目，以便生成在 Azure IaaS 群集上运行的 SOA 客户端应用程序。

### <a name="use-http-binding-with-azure-storage-queue"></a>在有 Azure 存储队列的情况下使用 Http 绑定
若要在有 Azure 存储队列的情况下使用 Http 绑定，需要对示例代码进行一些更改。

* 更新群集名称。
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* （可选）在 SessionStartInfo 中使用默认 TransportScheme 或显式将其设置为 Http。

```
    info.TransportScheme = TransportScheme.Http;
```

* 对 BrokerClient 使用默认绑定。
  
    ```
  // Before
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
  // After
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
  ```
  
    或者，显式使用 basicHttpBinding 进行设置。
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* （可选）在 SessionStartInfo 中将 UseAzureQueue 标志设置为 true。 如果未设置，则在群集名称具有 Azure 域后缀并且 TransportScheme 为 Http 时，默认情况下它将设置为 true。
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>在没有 Azure 存储队列的情况下使用 Http 绑定
要在没有 Azure 存储队列的情况下使用 Http 绑定，请在 SessionStartInfo 中将 UseAzureQueue 标志显式设置为 false。

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>使用 NetTcp 绑定
若要使用 NetTcp 绑定，配置与连接到本地群集时类似。 需要在头节点 VM 上打开几个终结点。 例如，如果已使用 HPC Pack IaaS 部署脚本创建群集，请在 Azure 门户中设置终结点，如下所示。

1. 停止 VM。
2. 分别为会话、代理、代理工作线程和数据服务添加 TCP 端口 9090、9087、9091、9094
   
    ![配置终结点][endpoint-new-portal]
3. 启动 VM。

除了将头名称变更为 IaaS 群集完整名称外，SOA 客户端应用程序不需要进行任何更改。

## <a name="next-steps"></a>后续步骤
* 有关使用 HPC Pack 运行 Excel 工作负荷的详细信息，请参阅[这些资源](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx)。
* 有关使用 HPC Pack 部署和管理 SOA 服务的详细信息，请参阅[在 Microsoft HPC Pack 中管理 SOA 服务](https://technet.microsoft.com/library/ff919412.aspx)。

<!--Image references-->
[scenario]: ./media/excel-cluster-hpcpack/scenario.png
[github]: ./media/excel-cluster-hpcpack/github.png
[template]: ./media/excel-cluster-hpcpack/template.png
[parameters]: ./media/excel-cluster-hpcpack/parameters.png
[parameters-new-portal]: ./media/excel-cluster-hpcpack/parameters-new-portal.png
[create]: ./media/excel-cluster-hpcpack/create.png
[connect]: ./media/excel-cluster-hpcpack/connect.png
[cert]: ./media/excel-cluster-hpcpack/cert.png
[addin]: ./media/excel-cluster-hpcpack/addin.png
[macro]: ./media/excel-cluster-hpcpack/macro.png
[options]: ./media/excel-cluster-hpcpack/options.png
[run]: ./media/excel-cluster-hpcpack/run.png
[endpoint]: ./media/excel-cluster-hpcpack/endpoint.png
[endpoint-new-portal]: ./media/excel-cluster-hpcpack/endpoint-new-portal.png
[udf]: ./media/excel-cluster-hpcpack/udf.png

