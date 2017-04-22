---

title: "基于 Azure Active Directory 组的许可的其他方案 | Microsoft 文档"
description: "有关基于 Azure Active Directory 组的许可的更多方案"
services: active-directory
keywords: "Azure AD 许可"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: a7240c52e9351a60e3cf577d8112862c7dc8d913
ms.lasthandoff: 03/14/2017


---

# <a name="scenarios-limitations-and-known-issues-with-using-groups-to-manage-licensing-in-azure-active-directory"></a>与在 Azure Active Directory 中使用组管理许可相关的方案、限制和已知问题

使用以下信息和示例更深入地了解 Azure Active Directory (Azure AD) 基于组的许可。

## <a name="use-group-based-licensing-with-dynamic-groups"></a>对动态组使用基于组的许可

可对任何安全组使用基于组的许可，这意味着，可将它与 Azure AD 动态组组合使用。 动态组针对用户对象属性运行规则，在组中自动添加和删除用户。

例如，可以创建多个动态组，为想要分配给用户的每组产品各创建一个。 每个组包含一个规则，该规则查找用户的特定属性，描述他们应该接收哪一组许可证。 可在本地分配属性并将它与 Azure AD 同步，或者直接在云中管理属性。

指定属性时，会将用户添加到其中一个或多个动态许可组。 片刻之后，即会将许可证分配给用户。 删除属性后，用户将离开组，许可证将被删除。

### <a name="example"></a>示例

假设某个本地标识管理解决方案可以确定用户应该有权访问哪些 Microsoft Web 服务。 该解决方案使用 **extensionAttribute1** 来存储表示用户应该具有的许可证的字符串值。 Azure AD Connect 会将该值与 Azure AD 同步。

对于本示例，目标是向用户分发 Office 365 企业版 E5 和企业移动性 + 安全性许可证。 在 Azure AD 中创建两个动态组，每个产品对应一个动态组。 这是因为某些用户可能只需要其中的一个产品，而不需要另一个产品。 然后在每个组中指定动态成员身份规则和许可证设置。 设置如下：

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 企业版 E5：基本服务

![Office 365 企业版 E5 基本服务的屏幕截图](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>企业移动性 + 安全性：许可的用户

![企业移动性 + 安全性许可的用户屏幕截图](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

本示例中修改了一个用户并将其 extensionAttribute1 设置为 `EMS;E5_baseservices;` 值。 这是因为我们希望此用户拥有上述两个许可证。 请注意，可在本地执行此项修改。 将更改与云同步后，该用户将自动添加到这两个组，然后获得了许可证。

![显示如何设置用户的 extensionAttribute1 的屏幕截图](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modify-a-dynamic-group-membership-rule"></a>修改动态组成员身份规则

修改现有组的成员身份规则时请小心。 更改规则时，将从组中删除所有用户。 将评估规则，然后根据新的条件将用户添加到该组。

如果为该组分配了许可证，则在此过程中，将删除所有拥有这些许可证的用户。 只有在评估新规则并重新添加用户后，才会应用新许可证。 这意味着，用户可能会丢失服务，在某些情况下，还会丢失数据。

最好不要更改组中用于分配许可证的成员身份规则， 而应该创建使用新规则的新组，然后像在原始组中一样指定许可证设置。 等到已添加成员并向所有用户应用许可证。 只有在完成此过程后，才可以删除原始组。 此方法可确保循序渐进地安全过渡到新的成员身份规则，而不会导致用户失去访问权限或丢失数据。


## <a name="multiple-groups-and-multiple-licenses"></a>多个组和多个许可证

用户可能是具有许可证的多个组的成员。 下面是需要注意的事项：

- 相同产品的多个许可证可能重叠，导致向用户应用所有已启用的服务。 以下示例显示了两个许可组：*E3 基本服务*包含首先向用户部署的基础服务。 *E3 扩展服务*包含只向某些用户部署的其他服务（Sway 和 Planner）。 请注意，Yammer 保持禁用供将来部署。 在本示例中，用户已添加到两个组：

  ![启用的服务的屏幕截图](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  因此，该用户获得了已启用产品的 12 个服务中的 7 个，同时只需为此产品使用 1 个许可证。

- 选择 *E3* 许可证可显示更多详细信息，包括有关哪些组导致为用户启用了哪些服务的信息。

  ![组启用的服务的屏幕截图](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>直接许可证与组许可证共存

如果用户从组继承许可证，则无法直接在用户对象中删除或修改该许可证分配。 所有更改必须在组中进行，然后由系统传播给所有用户。

但是，除了继承的许可证以外，还可以将相同的产品许可证直接分配给用户。 例如，使用这种方式可以做到只为一个用户启用产品中的其他服务，而不会影响到其他用户。

可以删除直接分配的许可证，但这样做不会影响继承的许可证。 例如，假设以下用户从某个组继承了 Office 365 企业版 E3 许可证。

1. 最初，该用户只从 *E3 基本服务*组继承许可证。 这样就会启用 4 个服务计划，如下图所示。

  ![E3 组启用的服务的屏幕截图](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. 单击“分配”可以直接向该用户分配 E3 许可证。 在本例中，我们要禁用除 Yammer Enterprise 以外的所有服务计划。

  ![演示如何直接向用户分配许可证的屏幕截图](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. 因此，该用户仍然只会使用 E3 产品的 1 个许可证。 但是，直接分配只会为该用户启用 Yammer Enterprise 服务。 下图显示组成员身份与直接分配分别会启用哪些服务。

  ![继承分配与直接分配的屏幕截图](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. 使用直接分配时，允许执行以下操作：

  a. 可在用户对象中直接关闭 Yammer Enterprise。 请注意，此服务的“开/关”切换器的启用方式与其他服务相反。 这是因为此服务是直接对用户启用的，因此可以修改。

  b. 也可以将其他服务启用为直接分配的许可证的一部分。

  c. 可以使用“删除”按钮删除用户的直接许可证。 可以看到，该用户现在只拥有继承的组许可证，并且只有原始服务保持启用状态。

    ![演示如何删除直接分配的屏幕截图](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>使用位置

某些 Microsoft 服务不能在所有位置使用。 在将许可证分配给用户之前，管理员必须为该用户指定“使用位置”属性。 可在 [Azure 门户](https://portal.azure.com)中的“用户”&gt;“配置文件”&gt;“设置”下面执行此操作。

对于组许可证分配，任何没有指定使用位置的用户将继承该目录的位置。 如果用户位于不同的位置，请在将用户添加到具有许可证的组之前，在用户对象中正确反映这种状态。

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>使用 PowerShell 查看谁拥有继承的许可证和直接许可证

在 Azure AD 版本预览期内，无法使用 PowerShell 来完全控制组许可证的分配。 但是，可以使用它来发现有关用户状态的基本信息，以及确定许可证是从组继承的还是直接分配的。 以下代码示例演示管理员如何生成有关许可证分配的基本报告。

1. 运行 `connect-msolservice` cmdlet 进行身份验证并连接到你的租户。

2. 可以使用 `Get-MsolAccountSku` 来发现该租户中所有已预配的产品许可证。

  ![Get-Msolaccountsku cmdlet 的屏幕截图](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. 在本示例中，我们希望找出哪些用户拥有直接分配的和/或从组继承的企业移动性 + 安全性许可证。 可以使用以下 PowerShell 脚本。
  
  ```
  #Returns TRUE if the user has the license assigned directly
  function UserHasLicenseAssignedDirectly
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
      foreach($license in $user.Licenses)
      {
          #we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              #This could be a group object or a user object (contrary to what the name suggests)
              #If the collection is empty, this means the license is assigned directly. This is the case for users who have never been licensed via groups in the past
              if ($license.GroupsAssigningLicense.Count -eq 0)
              {
                  return $true
              }
              #If the collection contains the ID of the user object, this means the license is assigned directly
              #Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach ($assignmentSource in $license.GroupsAssigningLicense)
              {
                  if ($assignmentSource -ieq $user.ObjectId)
                  {
                      return $true
                  }
              }
              return $false
          }
      }
      return $false
  }
  #Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
     foreach($license in $user.Licenses
     {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
          #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
          #This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
          {
                  #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  #Note: the license may also be assigned directly in addition to being inherited
                  if ($assignmentSource -ine $user.ObjectId)

            {
                      return $true
            }
          }
              return $false
        }
      }
      return $false
  }
  ```

4. 该脚本的余下部分将获取所有用户，针对每个用户执行这些函数。 然后以表的格式提供输出。
    
  ```
  #the license SKU we are interested in
  $skuId = "reseller-account:EMS"
  #find all users that have the SKU license assigned
  Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
      ObjectId, `
      @{Name="SkuId";Expression={$skuId}}, `
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
  ```

5. 完整的脚本输出如下所示：

  ![PowerShell 脚本输出的屏幕截图](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>限制和已知问题

如果使用基于组的许可，最好是熟悉以下限制和已知问题的列表。

- 基于组的许可目前不支持“嵌套组”（包含其他组的组）。 如果将许可证应用到某个嵌套组，只会向该组的直接一级用户成员应用许可证。

- 无法修改一个或多个许可证，因为它们是从组成员身份继承的。 若要查看或修改基于组的许可证，请访问 Azure 管理门户。

- [Office 365 管理门户](https://portal.office.com )目前不支持基于组的许可。 如果用户从组继承许可证，此许可证将在 Office 管理门户中显示为普通的用户许可证。 如果尝试修改该许可证（例如，禁用许可证中的服务，或尝试删除许可证），门户将返回错误消息。 无法直接修改用户的继承的组许可证。

- 如果从组中删除某个用户，导致其丢失许可证，则该许可证中的服务计划（例如 Exchange Online 或 SharePoint Online）将设置为“挂起”状态。 服务计划不会设置为最终已禁用状态。 这是一项预防措施，目的是避免管理员在执行组成员身份管理期间由于失误而意外删除用户数据。

- 如果针对极大型的组（例如，100,000 个用户）分配或修改许可证，可能会影响性能。 具体而言，Azure AD 自动化生成的大量更改可能会对 Azure AD 与本地系统之间的目录同步性能产生负面影响。 这可能会导致环境中的目录同步出现延迟。

- 许可证管理自动化不会自动根据环境中的所有更改类型做出反应。 例如，你可能会用完许可证，导致某些用户进入错误状态。 若要释放可用的许可座席计数，可以删除其他用户的某些直接分配的许可证。 但是，系统不会自动对此项更改做出反应，也不会修复处于该错误状态的用户。

  此类限制的解决方法之一是转到 Azure AD 中的“组”边栏选项卡，然后单击“重新处理”。 在可能的情况下，这种方法将会处理该组中的所有用户并解决错误状态。

## <a name="next-steps"></a>后续步骤

若要详细了解通过基于组的许可进行许可证管理的其他方案，请参阅：

* [Azure Active Directory 中基于组的许可是什么？](active-directory-licensing-whatis-azure-portal.md)
* [将许可证分配到 Azure Active Directory 中的组](active-directory-licensing-group-assignment-azure-portal.md)
* [识别和解决 Azure Active Directory 中组的许可问题](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [如何将单个许可用户迁移到 Azure Active Directory 中基于组的许可](active-directory-licensing-group-migration-azure-portal.md)

