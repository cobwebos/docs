---
title: Azure AD Connect：组写回
description: 本文介绍 Azure AD Connect 中的组写回。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2e24246c749978cd2bbb5b3d0821eea6d7dfb4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660870"
---
# <a name="azure-ad-connect-group-writeback"></a>Azure AD Connect 组写回

组写回功能使客户能够利用云组满足其混合需求。 如果使用 "Microsoft 365 组" 功能，则可以在本地 Active Directory 中显示这些组。 仅当本地 Active Directory 中存在 Exchange 时，此选项 **才** 可用。

## <a name="pre-requisites"></a>先决条件
必须满足以下先决条件才能启用组写回。
- 为租户 Azure Active Directory Premium 许可证。
- 已在 Exchange 内部部署组织与 Microsoft 365 之间配置的混合部署，并验证其是否正常运行。
- 在本地安装了受支持版本的 Exchange
- 使用 Azure Active Directory Connect 配置的单一登录 

## <a name="enable-group-writeback"></a>启用组写回
若要启用组写回，请使用以下步骤：

1. 打开 "Azure AD Connect" 向导，选择 " **配置** "，然后单击 " **下一步**"。
2. 选择 " **自定义同步选项** "，然后单击 " **下一步**"。
3. 在 " **连接到 Azure AD** " 页上，输入你的凭据。 单击“下一步”。
4. 在 " **可选功能** " 页上，验证先前配置的选项是否仍然处于选中状态。
5. 选择 " **组写回** "，然后单击 " **下一步**"。
6. 在 " **写回" 页**上，选择一个 Active Directory 组织单位 (OU) 存储从 Microsoft 365 同步到本地组织的对象，然后单击 " **下一步**"。
7. 在 "已 **准备好** 配置" 页上，单击 " **配置**"。
8. 向导完成后，单击 "配置完成" 页上的 " **退出** "。
9. 在 Azure Active Directory Connect 服务器上以管理员身份打开 Windows PowerShell，并运行以下命令。

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN
```

有关配置 Microsoft 365 组的其他信息，请参阅 [配置具有本地 Exchange 混合的 Microsoft 365 组](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect)。

## <a name="disabling-group-writeback"></a>禁用组写回
若要禁用组写回，请使用以下步骤： 


1. 启动 Azure Active Directory Connect 向导并导航到 "其他任务" 页。 选择 " **自定义同步选项** " 任务，然后单击 " **下一步**"。
2. 在 " **可选功能** " 页上，取消选中 "组写回"。  您将收到一条警告消息，告知组将被删除。  单击 **“是”** 。
   >[!IMPORTANT]
   > 禁用组写回会导致在下一同步周期中从本地 Active Directory 中删除以前由此功能创建的所有组。 

   ![取消选中框](media/how-to-connect-group-writeback/group2.png)
  
3. 单击“下一步”。
4. 单击 **“配置”** 。

 >[!NOTE]
 > 禁用组写回会将 "完全导入" 和 "完全同步" 标志设置为 "true" （在 Azure Active Directory 连接器上），从而导致规则更改在下一个同步周期中传播，并删除以前写回 Active Directory 的组。

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。