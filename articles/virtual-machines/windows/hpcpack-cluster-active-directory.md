---
title: "HPC Pack 群集与 Azure Active Directory 配合使用 | Microsoft Docs"
description: "了解如何将 Azure 中的 HPC Pack 2016 群集与 Azure Active Directory 集成"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/14/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: c5a06a9c810349b1bcce01c7f73563941a5af0ed
ms.lasthandoff: 03/31/2017


---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>使用 Azure Active Directory 在 Azure 中管理 HPC Pack 群集
对于在 Azure 中部署 HPC Pack 群集的管理员，[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) 支持与 [Azure Active Directory](../../active-directory/index.md) (Azure AD) 的集成。



对于以下高级任务，请按照本文中的步骤操作： 
* 将 HPC Pack 群集与 Azure AD 租户手动集成
* 在 Azure 中管理和计划 HPC Pack 群集中的作业 

将 HPC Pack 群集解决方案与 Azure AD 集成按照标准步骤集成其他应用程序和服务。 本文假设你熟悉 Azure AD 中的基本用户管理。 有关详细信息和背景，请参阅 [Azure Active Directory 文档](../../active-directory/index.md)和以下部分。

## <a name="benefits-of-integration"></a>集成的好处


Azure Active Directory (Azure AD) 是基于多租户云的目录和标识管理服务，可提供对云解决方案的单一登录 (SSO) 访问。

HPC Pack 群集与 Azure AD 的集成可帮助实现以下目标：

* 从 HPC Pack 群集中删除传统的 Active Directory 域控制器。 这可以帮助减少维护群集的成本（如果这对于业务是不必要的）并加速执行部署过程。
* 利用 Azure AD 带来的以下好处：
    *   单一登录 
    *   在 Azure 中为 HPC Pack 群集使用本地 AD 标识 

    ![Azure Active Directory 环境](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>先决条件
* **在 Azure 虚拟计算机中部署的 HPC Pack 2016 群集** - 有关步骤，请参阅[在 Azure 中部署 HPC Pack 2016 群集](hpcpack-2016-cluster.md)。 需要获得头节点的 DNS 名称和群集管理员的凭据才能完成本文中的步骤。

  > [!NOTE]
  > Azure Active Directory 集成在 HPC Pack 2016 之前的 HPC Pack 版本中不受支持。



* **客户端计算机** - 需要 Windows 或 Windows Server 客户端计算机才能运行 HPC Pack 客户端实用工具。 如果你只想要使用 HPC Pack Web 门户或 REST API 来提交作业，则可以使用自选的任意客户端计算机。

* **HPC Pack 客户端实用工具** - 使用 Microsoft 下载中心提供的免费安装包将 HPC Pack 客户端实用工具安装在客户端计算机上。


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>步骤 1：将 HPC 群集服务器注册到 Azure AD 租户
1. 登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左侧菜单中单击“Active Directory”，然后在订阅中单击所需的目录。 必须具有访问目录中的资源的权限。
3. 单击“用户”，并确保存在已经创建或配置的用户帐户。
4. 依次单击“应用程序” > “添加”，然后单击“添加我的组织正在开发的应用程序”。 在向导中输入以下信息：
    * **名称** - HPCPackClusterServer
    * **类型**：选择“Web 应用程序和/或 Web API”
    * **登录 URL** - 示例的基 URL，默认情况下为 `https://hpcserver`
    * **应用 ID URI** - `https://<Directory_name>/<application_name>`。 例如，将 `<Directory_name`> 替换为 Azure AD 租户的全名 `hpclocal.onmicrosoft.com`，并将 `<application_name>` 替换为之前选择的名称。

5. 添加应用后，单击“配置”。 配置以下属性：
    * 为“应用程序是多租户的”选择“是”
    * 为“访问应用需要进行用户分配”选择“是”。

6. 单击“保存” 。 保存完成时，请单击“管理清单”。 此操作下载应用程序的清单 JavaScript 对象表示法 (JSON) 文件。 通过查找 `appRoles` 并添加以下应用程序角色编辑下载的清单：
    ```json
    "appRoles": [
        {
        "allowedMemberTypes": [
            "User",
            "Application"
        ],
        "displayName": "HpcAdminMirror",
        "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
        "isEnabled": true,
        "description": "HpcAdminMirror",
        "value": "HpcAdminMirror"
        },
        {
        "allowedMemberTypes": [
            "User",
            "Application"
        ],
        "description": "HpcUsers",
        "displayName": "HpcUsers",
        "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
        "isEnabled": true,
        "value": "HpcUsers"
        }
    ],
    ```
7. 保存文件。 然后在门户中，依次单击“管理清单” > “上传清单”。 然后可以上传已编辑的清单。
8. 单击“用户”、选择用户，然后单击“分配”。 将可用角色之一（HpcUsers 或 HpcAdminMirror）分配给该用户。 为目录中的其他用户重复此步骤。 有关群集用户的背景信息，请参阅[管理群集用户](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx)。

   > [!NOTE] 
   > 为了管理用户，我们建议使用 [Azure 门户](https://portal.azure.com)中的“Azure Active Directory 预览”边栏选项卡。
   >


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>步骤 2：将 HPC 群集客户端注册到 Azure AD 租户

1. 登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左侧菜单中单击“Active Directory”，然后在订阅中单击所需的目录。 必须具有访问目录中的资源的权限。
3. 依次单击“应用程序” > “添加”，然后单击“添加我的组织正在开发的应用程序”。 在向导中输入以下信息：

    * **名称** - HPCPackClusterClient
    * **类型** - 选择“本机客户端应用程序”
    * **重定向 URI** - `http://hpcclient`

4. 添加应用后，单击“配置”。 复制“客户端 ID”值并保存它。 稍后在配置应用程序时需要此值。

5. 在“针对其他应用程序的权限”中，单击“添加应用程序”。 搜索并添加 HpcPackClusterServer 应用程序（在步骤 1 中创建）。

6. 在“委派权限”下拉列表中，选择“访问 HpcClusterServer”。 。


## <a name="step-3-configure-the-hpc-cluster"></a>步骤 3：配置 HPC 群集

1. 在 Azure 中连接到 HPC Pack 2016 头节点。

2. 启动 HPC PowerShell。

3. 运行以下命令：

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    其中

    * `AADTenant` 指定 Azure AD 租户名称，例如 `hpclocal.onmicrosoft.com`
    * `AADClientAppId` 指定在步骤 2 中创建的应用的客户端 ID。

4. 重新启动 HpcSchedulerStateful 服务。

    在带有多个头节点的群集中，可以在头节点上运行以下 PowerShell 命令以切换 HpcSchedulerStateful 服务的主副本：

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName “fabric:/HpcApplication/SchedulerStatefulService”

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>步骤 4：从客户端管理和提交作业

若要在计算机上安装 HPC Pack 客户端实用工具，请从 Microsoft 下载中心下载 HPC Pack 2016 安装程序文件（完整安装）。 开始安装时，请选择针对 **HPC Pack 客户端实用工具**的安装选项。

若要准备客户端计算机，请在客户端计算机上安装在 [HPC 群集安装](hpcpack-2016-cluster.md)期间使用的证书。 使用标准 Windows 证书管理过程将公用证书安装到“证书 - 当前用户”“受信任根证书颁发机构” > 存储。 

现在可通过 Azure AD 帐户运行 HPC Pack 命令或使用 HPC Pack 作业管理器 GUI 提交和管理群集作业。 有关作业提交选项，请参阅[将 HPC 作业提交到 Azure 中的 HPC Pack 群集](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster)。

> [!NOTE]
> 首次在 Azure 中尝试连接到 HPC Pack 群集时，将显示弹出窗口。 输入 Azure AD 凭据登录。 然后缓存令牌。 之后在 Azure 中连接到群集时将使用缓存的令牌，除非身份验证更改或清除内容。
>
  
例如，完成之前的步骤后，可以从本地客户端查询作业，如下所示：

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>用于使用 Azure AD 集成提交作业的有用 cmdlet 

### <a name="manage-the-local-token-cache"></a>管理本地令牌缓存

HPC Pack 2016 提供两个新的 HPC PowerShell cmdlet 用于管理本地令牌缓存。 这些 cmdlet 对于以非交互方式提交作业很有用。 请参阅以下示例：

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>设置使用 Azure AD 帐户提交作业的凭据 

有时，需要在 HPC 群集用户下运行作业（对于已加入域的 HPC 群集，以域用户身份运行；对于未加入域的 HPC 群集，以头节点上的本地用户身份运行）。

1. 使用以下命令设置凭据：

    ```powershell
    $localUser = “<username>”

    $localUserPassword=”<password>”

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. 然后按如下所示提交作业。 作业/任务在计算节点上的 $localUser 下运行。

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   如果未使用 `Submit-HpcJob` 指定 `–Credential`，则作业/任务以 Azure AD 帐户身份在本地映射的用户下运行。 （HPC 群集使用与 Azure AD 帐户相同的名称创建本地用户以运行任务。）
    
3. 设置 Azure AD 帐户的已扩展数据。 在使用 Azure AD 帐户在 Linux 节点上运行 MPI 作业时，这十分有用。

   * 设置 Azure AD 帐户自身的已扩展数据

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * 设置已扩展数据，并以 HPC 群集用户身份运行
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```


