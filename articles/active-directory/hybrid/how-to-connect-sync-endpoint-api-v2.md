---
title: Azure AD Connect 同步 V2 终结点公共预览版 | Microsoft Docs
description: 本文档介绍 Azure AD Connect 同步 v2 终结点 API 的更新。
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 779b29c8d31dffa495926a7f2ca5e1f77870078c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319905"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Azure AD Connect 同步 V2 终结点 API（公共预览版） 
Microsoft 已部署新的 Azure AD Connect 终结点 (API)，可提高 Azure Active Directory 的同步服务操作性能。 通过利用新的 V2 终结点，导出或导入 Azure AD 时的性能会有显著提升。 这一新终结点支持以下功能：
    
 -  同步具有最多 25 万名成员的组
 - 提高导出和导入到 Azure AD 的性能
 
> [!NOTE]
> 目前，新终结点对写回的 Microsoft 365 组没有已配置的组大小限制。 这可能会影响 Active Directory 和同步周期延迟。 建议以递增方式增加组大小。  


## <a name="pre-requisites"></a>先决条件  
为使用新的 V2 终结点，需要使用 [Azure AD Connect 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) 或更高版本，并按照以下部署步骤为 Azure AD Connect 服务器启用 V2 终结点。   

>[!NOTE]
>目前，此公共预览版仅适用于 Azure 全球云，不适用于[国家云](../develop/authentication-national-cloud.md)。

### <a name="public-preview-limitations"></a>公共预览版限制  
尽管此版本已经过大量测试，但仍然可能会遇到问题。 此公共预览版的目标之一就是查找并修复任何此类问题。  

>[!IMPORTANT]
> 尽管为此公共预览版提供了支持，但 Microsoft 可能并不能每次都能够立即解决你可能遇到的所有问题。 因此，建议先做出最佳判断，然后再在生产环境中部署此版本。 

## <a name="deployment-guidance"></a>部署指南 
需要部署 [Azure AD Connect 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) 或更高版本才能使用 V2 终结点。 使用提供的链接进行下载。 

建议按照[交叉迁移](./how-to-upgrade-previous-version.md#swing-migration)方法在环境中推出新的终结点。 这将在事件中提供清晰的应变计划，即主要回滚必不可少。 以下示例说明如何在这种情况下使用交叉迁移。 有关交叉迁移部署方法的详细信息，请参考提供的链接。 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>用于部署 V2 终结点的交叉迁移
以下步骤将指导你完成使用交叉方法部署 v2 终结点。

1. 在当前过渡服务器上部署 V2 终结点。 在以下步骤中，此服务器将称为 V2 服务器。 当前活动服务器将继续使用 V1 终结点处理生产工作负载，该服务器将称为 V1 服务器。
1. 验证 V2 服务器是否仍按预期处理导入。 在此阶段，不会将大型组预配到 Azure AD 或本地 AD，但可以验证升级是否不会对现有同步过程造成其他任何意外影响。 
2. 验证完成后，将 V2 服务器切换为活动服务器，将 V1 服务器切换为过渡服务器 。 此时，如果启用了组写回功能，则在要同步的范围内的大型组将预配到 Azure AD，并且大型 Microsoft 365 统一组将预配到 AD。
3. 验证 V2 服务器是否可以成功执行和处理大型组。 可以选择停留在此步骤，并监视同步过程一段时间。
  >[!NOTE]
  > 如果需要转换回之前的配置，可以从 V2 服务器交叉迁移回 V1 服务器 。 由于 V1 终结点不支持成员数超过 5 万的组，因此随后将删除 Azure AD 或本地 AD 中由 Azure AD Connect 预配的任何大型组。 
4. 确定要使用 V2 终结点时，升级 V1 服务器以开始使用 V2 终结点。 
 

## <a name="expectations-of-performance-impact"></a>性能影响的预期  
使用 V2 终结点时，性能提升取决于同步组的数量、大小及其改动（将用户添加为组成员以及将用户从组成员中删除而产生的活动）。 在不增加同步组数量、大小或改动的情况下，使用新的终结点应该可以缩短导出和导入到 Azure AD 所需的时间。 
 
但同步大型组时，所需的额外处理可能会抵消性能提升。 在同步过程中添加过多大型组，最终可能会增加整体同步时间。  

为更好地了解添加新组将如何影响同步性能，建议先只同步成员数少于 10 万的少量大型组。 然后，可以通过 OU、属性或最大组大小筛选来引入更多的组，增加组的数量和大小。 Azure AD 连接器（而不是本地 AD 连接器）的导出和导入任务将实现性能改进。 

## <a name="deployment-step-by-step"></a>逐步部署 
以下三个阶段是部署新 V2 终结点的详细示例。  使用这些阶段作为部署指南。

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>阶段 1 - 安装和验证 Azure AD Connect 
建议先安装或升级到 [Azure AD Connect 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) 或更高版本并验证同步过程，然后开始第二阶段，启用 V2 终结点。 在 Azure AD Connect 服务器上： 


1. [可选] 执行数据库备份 
2. 安装或升级到 [Azure AD Connect 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) 或更高版本。
3. 验证安装 

### <a name="phase-2--enable-the-v2-endpoint"></a>阶段 2 - 启用 V2 终结点 
下一步是启用 V2 终结点。 

> [!NOTE]
> 为服务器启用 V2 终结点后，可以看到现有工作负载有一些性能改进。 不过，你仍将无法同步成员数超过 5 万的组。 

要切换到 V2 终结点，请执行以下步骤： 

1. 以管理员身份打开 PowerShell 命令提示符。 
2. 确认没有同步操作正在运行后，禁用同步计划程序： 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. 导入新模块： 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  切换到 v2 终结点：

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
你现在已为服务器启用了 V2 终结点。 请花一些时间验证启用 V2 终结点后是否存在意外结果，然后再进行下一阶段，提高组大小限制。 
>[!NOTE]
>根据安装 Azure AD Connect 时所获得的安装路径，文件/模块路径可能会使用不同的驱动器号。 


### <a name="phase-3--increase-the-group-membership-limit"></a>阶段 3 - 提高组成员身份限制 
确认服务正在运行且未出现意外结果之后，可以继续提高组成员身份限制。 建议先将成员身份限制提高到稍高的值 （例如 7.5 万成员），以查看较大的组同步到 Azure AD 时的情况。 如果对结果满意，可以进一步提高成员限制。  

最大限制为每组 25 万个成员。 

可以按以下步骤提高成员身份限制：  

1. 打开 Azure AD 同步规则编辑器 
2. 在编辑器中，选择方向“出站” 
3. 单击“出站到 AAD - 组加入”同步规则 
4. 单击 " **编辑** " 按钮 ![ 屏幕截图，其中已选中 "Out To AAD-Group Join"。](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. 单击“确定”按钮，禁用默认规则并创建可编辑的副本。
 ![屏幕截图，显示 "编辑保留的规则确认" 窗口，并选中 "是" 按钮。](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. 在 " **说明** " 页上的弹出窗口中，将优先级设置为介于1和99屏幕快照之间的可用值， ![ 这将显示 "编辑出站同步规则" 窗口，其中突出显示了 "优先级"。](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. 在“转换”页面上，更新“成员”转换的“源”值，将 50000 替换为介于 50001 和 250000 之间的值  。 此替换会增加将同步到 Azure AD 的组的最大成员身份大小。 建议从 10 万开始，以了解同步大型组将对同步性能产生的影响。 
 
 **示例** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![编辑同步规则](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. 点击“保存”(Save) 
10. 打开管理员 PowerShell 提示符 
11. 重新启用同步计划程序 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> 如果未启用 Azure AD Connect Health，请将 Windows 应用程序事件日志设置更改为存档日志，而不是覆盖日志。 这些日志可能有助于后续的故障排除工作。 

>[!NOTE]
> 启用新的终结点之后，AAD 连接器上可能会显示名为“dn-attributes-failure”的其他导出错误。 ID 为 6949 的每个错误都有相应的事件日志条目。 这些错误是信息性的，并不表示存在安装问题，而是表示由于成员对象本身未同步到 Azure AD，因此同步过程无法将某些成员添加到 Azure AD 中的组。 

新的 V2 终结点代码处理某些导出错误类型的方式与 V1 代码的处理方式稍有不同。  使用 V2 终结点时，可能会显示更多的信息性错误消息。 

>[!NOTE]
> 升级 Azure AD Connect 时，确保重新运行阶段 2 中的步骤，因为在升级过程中不会保留这些更改。 

随后在“出站到 AAD - 组加入”同步规则中提高组成员限制期间，不需要完全同步，因此可以通过在 PowerShell 中运行以下命令来选择取消完全同步。 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> 如果 Microsoft 365 统一组的成员超过 5 万，则这些组将读取到 Azure AD Connect 中，并且如果启用了组写回功能，这些组将写入到本地 AD 中。 

## <a name="rollback"></a>回退 
如果启用了 v2 终结点并且需要回滚，请执行以下步骤： 

1. 在 Azure AD Connect 服务器上：a. [可选] 执行数据库备份 
2. 打开管理员 PowerShell 提示符：
3. 确认没有同步操作正在运行后，禁用同步计划程序
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. 切换到 V1 终结点* 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. 打开 Azure AD 同步规则编辑器 
6. 删除“出站到 AAD - 组加入”同步规则的可编辑副本 
7. 启用“出站到 AAD - 组加入”同步规则的默认副本 
8. 打开管理员 PowerShell 提示符 
9. 重新启用同步计划程序 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> 从 V2 切换回 V1 终结点时，在运行完全同步后，将删除与 5 万名以上的成员同步的组（适用于预配到 Azure AD 的 AD 组和预配到 AD 的 Microsoft 365 统一组）。 

## <a name="frequently-asked-questions"></a>常见问题  
**问：** 客户是否可以在生产中使用此功能？  
</br>是，可以在生产环境中使用此功能，如前文所述。
 
**问：** 如果出现问题，客户可以联系谁？  
</br>如果使用此功能时需要支持，则应打开支持案例。 
 
**问：** 能否经常更新公共预览版？  
</br>公开预览期间更改次数有限。 在生产中部署公共预览版功能时，应评估此风险。  
 
**问：** 何时改版？  
</br>改版之前，可能会取消并重新设计公共预览版功能。  
 
## <a name="next-steps"></a>后续步骤

* [Azure AD Connect 同步：理解和自定义同步](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)