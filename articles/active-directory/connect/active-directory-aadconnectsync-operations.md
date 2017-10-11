---
title: "Azure AD Connect 同步：操作任务和注意事项 | Microsoft Docs"
description: "本主题介绍 Azure AD Connect 同步的操作任务，以及如何准备运行此组件。"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: b7583a1556bb1113f349a78890768451e39c6878
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Azure AD Connect 同步：操作任务和注意事项
本主题旨在介绍 Azure AD Connect 同步的操作任务。

## <a name="staging-mode"></a>过渡模式
暂存模式可以用于许多方案，包括：

* 高可用性。
* 测试和部署新的配置更改。
* 引入新的服务器并解除旧服务器。

使用处于暂存模式的服务器，可以在激活服务器之前更改配置和预览更改。 它还允许运行完全导入和完全同步，以便在生产环境中应用所有更改之前验证这些更改是否符合预期。

可以在安装过程中选择将服务器置于**暂存模式**。 此操作可以激活服务器进行导入和同步，但不会运行任何导出。 处于暂存模式的服务器不会运行密码同步或密码写回，即使在安装期间选择了这些功能。 如果禁用暂存模式，服务器将开始导出，启用密码同步，并启用密码写回。

仍然可以使用 Synchronization Service Manager 强制导出。

处于暂存模式的服务器继续接收来自 Active Directory 和 Azure AD 的更改。 它始终都有最新更改的副本，并且可以非常快速地接管另一服务器的责任。 如果对主要服务器进行配置更改，则需要负责对处于暂存模式的服务器进行相同的更改。

对于熟悉旧式同步技术的人员而言，暂存模式是不同的，因为服务器有自身的 SQL 数据库。 此体系结构允许将暂存模式服务器放置在不同的数据中心。

### <a name="verify-the-configuration-of-a-server"></a>验证服务器的配置
若要应用此方法，请遵循以下步骤：

1. [准备](#prepare)
2. [配置](#configuration)
3. [导入和同步](#import-and-synchronize)
4. [验证](#verify)
5. [切换活动服务器](#switch-active-server)

#### <a name="prepare"></a>准备
1. 安装 Azure AD Connect，选择“暂存模式”，并取消选择安装向导中最后一页上的“启动同步”。 此模式允许手动运行同步引擎。
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. 注销/登录并从“开始”菜单选择“同步服务”。

#### <a name="configuration"></a>配置
如果对主服务器进行了自定义更改并希望比较配置和临时服务器，则使用 [Azure AD Connect 配置文档管理器](https://github.com/Microsoft/AADConnectConfigDocumenter)。

#### <a name="import-and-synchronize"></a>导入和同步
1. 选择“连接器”，并选择第一个 **Active Directory 域服务**类型的连接器。 单击“运行”，并依次选择“完全导入”和“确定”。 针对此类型的所有连接器执行这些步骤。
2. 选择 **Azure Active Directory (Microsoft)** 类型的连接器。 单击“运行”，并依次选择“完全导入”和“确定”。
3. 确保“连接器”选项卡仍处于选中状态。 针对每个 **Active Directory 域服务**类型的连接器单击“运行”，并选择“差异同步”和“确定”。
4. 选择 **Azure Active Directory (Microsoft)** 类型的连接器。 单击“运行”，选择“差异同步”，并选择“确定”。

现在，已将导出更改暂存到 Azure AD 和本地 AD（如果正在使用 Exchange 混合部署）。 使用后续步骤可在实际开始导出到目录之前，检查将要更改的内容。

#### <a name="verify"></a>验证
1. 启动 cmd 提示符并转到 `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. 运行：`csexport "Name of Connector" %temp%\export.xml /f:x` 连接器名称可以在同步服务中找到。 它的名称类似于“contoso.com – AAD”（表示 Azure AD）。
3. 将 PowerShell 脚本从 [CSAnalyzer](#appendix-csanalyzer) 部分复制到名为 `csanalyzer.ps1` 的文件。
4. 打开 PowerShell 窗口并浏览到已在其中创建 PowerShell 脚本的文件夹。
5. 运行：`.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`。
6. 现在已经有名为 **processedusers1.csv** 的文件，可在 Microsoft Excel 中检查。 可在此文件中找到要导出到 Azure AD 的处于暂存状态的所有更改。
7. 对数据或配置进行必要的更改并再次运行这些步骤（导入和同步和身份验证），直到要导出的更改都按预期进行。

#### <a name="switch-active-server"></a>切换活动服务器
1. 在当前处于活动状态的服务器上，关闭服务器 (DirSync/FIM/Azure AD Sync)，使它不会导出到 Azure AD，或将它设为暂存模式 (Azure AD Connect)。
2. 在处于“暂存模式”的服务器上运行安装向导，并禁用“暂存模式”。
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>灾难恢复
实现设计中规划了在灾难中失去同步服务器时的应对方法。 有不同的模型可用，要使用哪一种模型取决于许多因素，包括：

* 停机期间无法对 Azure AD 中的对象进行更改的容限度如何？
* 如果使用密码同步，用户是否接受他们在本地更改时必须在 Azure AD 中使用旧密码？
* 是否对实时操作具有依赖性，例如密码写回？

根据这些问题的回答和组织的策略，实施下列其中一个策略：

* 根据需要重建。
* 具有备用的待机服务器（称为**暂存模式**）。
* 使用虚拟机

如果不使用内置的 SQL Express 数据库，应查看 [SQL 高可用性](#sql-high-availability)部分。

### <a name="rebuild-when-needed"></a>根据需要重建
必要时规划服务器重建为可行的策略。 通常，在几个小时内即可完成安装同步引擎以及执行初始导入和同步。 如果没有可用的备用服务器，则可以暂时使用域控制器托管同步引擎。

同步引擎服务器不存储有关对象的任何状态，因此可以从 Active Directory 与 Azure AD 中的数据重建数据库。 **sourceAnchor** 属性可用于联接来自本地和云的对象。 如果重新生成包含本地与云中现有对象的服务器，同步引擎的重新安装符合这些项目。 需要记录和保存的内容是对服务器进行的配置更改，例如筛选和同步规则。 在开始同步之前，必须重新应用这些自定义配置。

### <a name="have-a-spare-standby-server---staging-mode"></a>具有备用的待机服务器 - 暂存模式
如果环境更复杂，我们建议使用一个或多个待机服务器。 可以在安装过程中启用服务器的**暂存模式**。

有关详细信息，请参阅[暂存模式](#staging-mode)。

### <a name="use-virtual-machines"></a>使用虚拟机
常用的受支持方法是在虚拟机中运行同步引擎。 如果主机有问题，可将包含同步引擎服务器的映像迁移到另一个服务器。

### <a name="sql-high-availability"></a>SQL 高可用性
如果未使用 Azure AD Connect 随附的 SQL Server Express，还应考虑 SQL Server 的高可用性。 支持的高可用性解决方案包括 SQL 群集和 AOA（Always On 可用性组）。 不支持的解决方案包括镜像。

Azure AD Connect 版本 1.1.524.0 中添加了对 SQL AOA 的支持。 安装 Azure AD Connect 之前，必须启用 SQL AOA。 在安装期间，Azure AD Connect 会检测是否已为提供的 SQL 实例启用 SQL AOA。 如果启用了 SQL AOA，Azure AD Connect 进一步指出如果 SQL AOA 配置为使用同步复制或异步复制。 当设置可用性组侦听器，建议将 RegisterAllProvidersIP 属性设置为 0。 这是因为 Azure AD Connect 当前使用 SQL Native Client 连接到 SQL 和 SQL Native Client 不支持使用 MultiSubNetFailover 属性。

## <a name="appendix-csanalyzer"></a>附录 CSAnalyzer
有关如何使用此脚本的信息，请参阅[验证](#verify)部分。

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as as CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>后续步骤
**概述主题**  

* [Azure AD Connect 同步：理解和自定义同步](active-directory-aadconnectsync-whatis.md)  
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)  
