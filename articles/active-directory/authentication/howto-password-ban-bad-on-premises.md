---
title: 部署 Azure AD 密码保护预览版
description: 部署 Azure AD 密码保护预览版，以禁止错误的本地密码
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 5065399f161bcaee2f9518236a28f0f5faa0ea5b
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902021"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>预览版：部署 Azure AD 密码保护

|     |
| --- |
| Azure AD 密码保护和自定义禁止密码列表是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

了解[如何为 Windows Server Active Directory 实施 Azure AD 密码保护](concept-password-ban-bad-on-premises.md)后，下一步是规划和执行部署。

## <a name="deployment-strategy"></a>部署策略

Microsoft 建议在审核模式下开始任何部署。 审核模式是默认的初始设置，在其中可以继续设置密码，如果阻止设置密码，会在事件日志中创建条目。 在审核模式下完全部署代理服务器和 DC 代理之后，应执行定期监视，以确定在实施密码策略后，对用户和环境造成了哪些影响。

在审核阶段，许多组织发现：

* 需要改进现有的操作过程，以使用更安全的密码。
* 用户经常选择不安全的密码。
* 需要告知用户即将对安全措施做出的更改以及这些更改对用户造成的影响，并帮助用户更好地了解如何选择更安全的密码。

在审核模式下运行该功能一段合理的时间后，可将实施配置从“审核”转换为“实施”，因而需要更安全的密码。 在此期间最好是执行有针对性的监视。

## <a name="known-limitation"></a>已知限制

Azure AD 密码保护代理预览版存在一个已知限制。 不支持使用需要 MFA 的租户管理员帐户。 将来的 Azure AD 密码保护代理更新版将支持使用需要 MFA 的管理员帐户进行代理注册。

## <a name="single-forest-deployment"></a>单林部署

使用代理服务在最多两台服务器上部署 Azure AD 密码保护预览版，可以增量方式将 DC 代理服务部署到 Active Directory 林中的所有域控制器。

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>下载软件

可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)下载 Azure AD 密码保护的两个必需安装程序

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>安装和配置 Azure AD 密码保护代理服务

1. 选择用于托管 Azure AD 密码保护代理服务的一个或多个服务器。
   * 每个此类服务只能为单个林提供密码策略，主机必须已加入该林中的某个域（根和子级受到同等支持）。 为使 Azure AD 密码保护代理服务发挥其作用，必须在林的每个域中的至少一个 DC，与 Azure AD 密码保护代理主机之间建立网络连接。
   * 支持在域控制器上安装并运行 Azure AD 密码保护代理服务用于测试，但这需要建立 Internet 连接。

   > [!NOTE]
   > 公共预览版支持对每个林最多部署两 (2) 个代理服务器。

2. 使用 AzureADPasswordProtectionProxy.msi MSI 包安装密码策略代理服务软件。
   * 安装该软件不需要重新启动。 可以使用标准的 MSI 过程将软件安装自动化，例如：`msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`

3. 以管理员身份打开 PowerShell 窗口。
   * Azure AD 密码保护代理软件包含名为 AzureADPasswordProtection 的新 PowerShell 模块。 以下步骤假设从 PowerShell 模块运行各个 cmdlet、已打开新的 PowerShell 窗口，并已导入新模块，如下所示：
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > 安装软件会修改主机的 PSModulePath 环境变量。 若要使此更改生效，以便可以导入和使用 AzureADPasswordProtection powershell 模块，可能需要打开全新的 PowerShell 控制台窗口。

   * 使用以下 PowerShell 命令检查服务是否正在运行：`Get-Service AzureADPasswordProtectionProxy | fl`。
      * 该命令应生成 **Status** 为“Running”的结果。

4. 注册代理。
   * 完成步骤 3 后，Azure AD 密码保护代理服务会在计算机上运行，但尚未获得用来与 Azure AD 通信的凭据。 必须注册到 Azure AD 才能使用 `Register-AzureADPasswordProtectionProxy` PowerShell cmdlet。 该 cmdlet 需要 Azure 租户的全局管理员凭据，以及在本地林根域中的 Active Directory 域管理员特权。 针对给定的代理服务成功运行该 cmdlet 后，对 `Register-AzureADPasswordProtectionProxy` 的其他调用会继续成功，但没有必要。
      * 可按如下所示运行该 cmdlet：

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         仅当当前登录的用户也是根域的 Active Directory 域管理员时，该示例才有效。 另一种做法是通过 `-ForestCredential` 参数提供所需的域凭据。

   > [!TIP]
   > 首次针对给定的 Azure 租户运行此 cmdlet 时，在 cmdlet 完成执行之前，可能会出现明显的延迟（数秒）。 除非报告了失败，否则应将这种延迟视为警告。

   > [!NOTE]
   > 在 Azure AD 密码保护代理服务的生存期，预期只需注册该服务一次。 注册后，代理服务会自动执行其他任何必要的维护。 针对给定的林成功运行该 cmdlet 后，对“Register-AzureADPasswordProtectionProxy”的其他调用会继续成功，但没有必要。

5. 注册林。
   * 必须运行 `Register-AzureADPasswordProtectionForest` Powershell cmdlet，使用用来与 Azure 通信的凭据将本地 Active Directory 初始化。 该 cmdlet 需要 Azure 租户的全局管理员凭据，以及在本地林根域中的 Active Directory 域管理员特权。 针对每个林运行此步骤一次。
      * 可按如下所示运行该 cmdlet：

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         仅当当前登录的用户也是根域的 Active Directory 域管理员时，该示例才有效。 另一种做法是通过 -ForestCredential 参数提供所需的域凭据。

         > [!NOTE]
         > 如果环境中安装了多个代理服务器，在上述过程中指定哪个代理服务器并不重要。

         > [!TIP]
         > 首次针对给定的 Azure 租户运行此 cmdlet 时，在 cmdlet 完成执行之前，可能会出现明显的延迟（数秒）。 除非报告了失败，否则应将这种延迟视为警告。

   > [!NOTE]
   > 在 Active Directory 林的生存期内，预期只需注册该林一次。 注册后，林中运行的域控制器代理会自动执行其他任何必要的维护。 针对给定的林成功运行该 cmdlet 后，对 `Register-AzureADPasswordProtectionForest` 的其他调用会继续成功，但没有必要。

6. 可选：将 Azure AD 密码保护代理服务配置为侦听特定的端口。
   * 域控制器上的 Azure AD 密码保护 DC 代理软件使用“基于 TCP 的 RPC”来与 Azure AD 密码保护代理服务通信。 默认情况下，Azure AD 密码保护密码策略代理服务会侦听任何可用的动态 RPC 终结点。 出于网络拓扑或防火墙要求，可根据需要将服务配置为侦听特定的 TCP 端口。
      * 若要将服务配置为在静态端口下运行，请使用 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet。
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > 必须停止并重启服务，才能让这些更改生效。

      * 若要将服务配置为在动态端口下运行，请使用相同的过程，但要将 StaticPort 重新设置为 0，如下所示：
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > 必须停止并重启服务，才能让这些更改生效。

   > [!NOTE]
   > 在端口配置中进行任何更改后，需要手动重启 Azure AD 密码保护代理服务。 进行这种性质的配置更改后，不需要重启域控制器上运行的 DC 代理服务软件。

   * 可以使用 `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet 查询当前的服务配置，如以下示例所示：

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>安装 Azure AD 密码保护 DC 代理服务

* 使用 `AzureADPasswordProtectionDCAgent.msi` MSI 包安装 Azure AD 密码保护 DC 代理服务软件：
   * 由于只能在重新启动后加载或卸载密码筛选器 dll（这是一项操作系统要求），在安装和卸载后需要重新启动软件安装。
   * 支持在不充当域控制器的计算机上安装 DC 代理服务。 在这种情况下，服务将会启动并运行，但在将计算机提升为域控制器之前，服务会一直处于非活动状态。

   可以使用标准的 MSI 过程将软件安装自动化，例如：`msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > 示例 msiexec 命令会导致立即重新启动；指定 `/norestart` 标志可避免重新启动。

将 Azure AD 密码保护 DC 代理软件安装在域控制器上并重新启动后，安装即告完成。 无需（也无法）进行其他配置。

## <a name="multiple-forest-deployments"></a>多林部署

跨多个林部署 Azure AD 密码保护不需要满足其他要求。 根据“单林部署”部分中所述单独配置每个林。 每个 Azure AD 密码保护代理只能支持它所加入到的林中的域控制器。 不管 Active Directory 信任配置如何，给定林中的 Azure AD 密码保护软件都不知道其他林中部署的 Azure AD 密码保护软件。

## <a name="read-only-domain-controllers"></a>只读域控制器

密码更改\设置永远不会在只读域控制器 (RODC) 中处理和保留；这些操作会转发到可写域控制器。 因此，无需在 RODC 上安装 DC 代理软件。

## <a name="high-availability"></a>高可用性

确保 Azure AD 密码保护的高可用性的主要考虑因素在于，当林中的域控制器尝试从 Azure 下载新策略或其他数据时，如何确保代理服务器的可用性。 公共预览版支持对每个林最多部署两个代理服务器。 在确定要调用哪个代理服务器时，每个 DC 代理将使用简单的轮循机制算法，并跳过无响应的代理服务器。
通过设计 DC 代理软件可以缓解与高可用性相关的常用问题。 DC 代理维护最近下载的密码策略的本地缓存。 即使所有已注册的代理服务器出于任何原因而不可用，DC 代理也仍会实施其缓存的密码策略。 在大型部署中，密码策略的合理更新频率通常是几天，而不是几小时或更短的时间更新一次。   因此，代理服务器的短暂中断不会给 Azure AD 密码保护功能的运行或其安全优势造成严重影响。

## <a name="next-steps"></a>后续步骤

在本地服务器上安装 Azure AD 密码保护所需的服务后，请完成[安装后的配置并收集报告信息](howto-password-ban-bad-on-premises-operations.md)，以完成部署。

[Azure AD 密码保护的概念性概述](concept-password-ban-bad-on-premises.md)
