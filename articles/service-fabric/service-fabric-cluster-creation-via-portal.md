---
title: 在 Azure 门户中创建 Service Fabric 群集
description: 了解如何使用 Azure 门户和 Azure Key Vault 在 Azure 中设置安全的 Service Fabric 群集。
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: 64a4c430cc7402419d64b77fdcc9a6389cf9de6d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792473"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>使用 Azure 门户在 Azure 中创建 Service Fabric 群集
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure 门户](service-fabric-cluster-creation-via-portal.md)
> 
> 

本指南逐步介绍如何使用 Azure 门户在 Azure 中设置 Service Fabric 群集（Linux 或 Windows）。 其中包括以下步骤：

* 通过 Azure 门户在 Azure 中创建群集。
* 使用证书对管理员进行身份验证。

> [!NOTE]
> 有关更高级的安全选项（例如使用 Azure Active Directory 进行用户身份验证和设置应用程序安全证书），请参阅[使用 Azure 资源管理器创建群集][create-cluster-arm]。
> 
> 

## <a name="cluster-security"></a>群集安全性 
证书在 Service Fabric 中用于提供身份验证和加密，为群集及其应用程序提供全方位的保护。 若要详细了解如何在 Service Fabric 中使用证书，请参阅 [Service Fabric 群集安全方案][service-fabric-cluster-security]。

如果这是你首次创建 Service Fabric 群集或要为测试工作负荷部署群集，可跳到下一节（在 Azure 门户中创建群集）并使系统生成运行测试工作负荷所需的证书  。 如果要为生产工作负荷设置群集，请继续阅读。

#### <a name="cluster-and-server-certificate-required"></a>群集和服务器证书（必需）
需要使用此证书来保护群集以及防止未经授权访问群集。 此证书通过多种方式保护群集：

* **群集身份验证：** 在群集联合的情况下对节点间的通信进行身份验证。 只有可以使用此证书自我证明身份的节点才能加入群集。
* **服务器身份验证：** 在管理客户端上对群集管理终结点进行身份验证，使管理客户端知道它正在与真正的群集通信。 此证书还通过 HTTPS 为 HTTPS 管理 API 和 Service Fabric Explorer 提供 TLS。

为满足这些用途，该证书必须符合以下要求：

* 证书必须包含私钥。
* 必须为密钥交换创建证书，并且该证书可导出到个人信息交换 (.pfx) 文件。
* 证书的使用者名称必须与访问 Service Fabric 群集使用的域相匹配  。 若要为群集的 HTTPS 管理终结点和 Service Fabric Explorer 提供 TLS，这是必需的。 无法从证书颁发机构（CA）获取`.cloudapp.azure.com`域的 TLS/SSL 证书。 获取群集的自定义域名。 在从 CA 请求证书时，该证书的使用者名称必须与用于群集的自定义域名匹配。

#### <a name="client-authentication-certificates"></a>客户端身份验证证书
其他客户端证书可对执行群集管理任务的管理员进行身份验证。 Service Fabric 有两个访问级别：**管理员**和**只读用户**。 至少应使用一个证书进行管理访问。 若要进行其他用户级别的访问，必须提供单独的证书。 有关访问角色的详细信息，请参阅 [role-based access control for Service Fabric clients][service-fabric-cluster-security-roles]（适用于 Service Fabric 客户端的基于角色的访问控制）。

无需将客户端身份验证证书上传到 Key Vault 即可使用 Service Fabric。 只需将这些证书提供给有权管理群集的用户。 

> [!NOTE]
> 建议使用 Azure Active Directory 对执行群集管理操作的客户端进行身份验证。 若要使用 Azure Active Directory，必须[使用 Azure 资源管理器创建群集][create-cluster-arm]。
> 
> 

#### <a name="application-certificates-optional"></a>应用程序证书（可选）
可以出于应用程序安全目的，在群集上安装任意数量的附加证书。 在创建群集之前，请考虑需要在节点上安装证书的应用程序安全方案，例如：

* 加密和解密应用程序配置值
* 在复制期间跨节点加密数据 

[通过 Azure 门户创建群集](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md)时，无法配置应用程序证书。 若要在设置群集时配置应用程序证书，必须[使用 Azure 资源管理器创建群集][create-cluster-arm]。 也可以在创建群集后将应用程序证书添加到群集。

## <a name="create-cluster-in-the-azure-portal"></a>在 Azure 门户中创建群集

创建生产群集以满足应用程序需要涉及一些规划，为了帮助你进行规划，强烈建议你阅读并了解 [Service Fabric 群集规划注意事项][service-fabric-cluster-capacity]文档。 

### <a name="search-for-the-service-fabric-cluster-resource"></a>搜索 Service Fabric 群集资源

登录到 [Azure 门户][azure-portal]。
单击“创建资源”以添加新的资源模板  。 在“  全部”下面的“  市场”中搜索 Service Fabric 群集模板。
从列表中选择“**Service Fabric 群集**”。

![在 Azure 门户中搜索 Service Fabric 群集模板。][SearchforServiceFabricClusterTemplate]

导航到“Service Fabric 群集”  边栏选项卡，单击“创建”  。

“创建 Service Fabric 群集”边栏选项卡包含以下四个步骤  ：

### <a name="1-basics"></a>1.基础知识
![创建新资源组的屏幕截图。][CreateRG]

在“基本信息”边栏选项卡中，需要提供群集的基本详细信息。

1. 输入群集的名称。
2. 输入 VM 远程桌面的**用户名**和**密码**。
3. 务必选择要将群集部署到的**订阅**，尤其是在拥有多个订阅时。
4. 创建一个**新的资源组**。 最好让它与群集同名，这样稍后就可以轻松找到它们，在尝试更改部署或删除群集时非常有用。
   
   > [!NOTE]
   > 尽管可以决定使用现有资源组，但最好还是创建新的资源组。 这样做可以轻松删除群集及其使用的所有资源。
   > 
   > 
5. 选择要在其中创建群集的**位置**。 如果计划使用已上传到 Key Vault 的现有证书，则必须使用 Key Vault 所在的区域。 

### <a name="2-cluster-configuration"></a>2.群集配置
![创建节点类型][CreateNodeType]

配置群集节点。 节点类型定义 VM 大小、VM 数目及其属性。 群集可以有不只一个节点类型，但主节点类型（在门户定义的第一个节点类型）必须至少有 5 个 VM，因为这是 Service Fabric 系统服务放置到的节点类型。 不需要配置“**放置属性**”，因为系统会自动添加了“NodeTypeName”的默认放置属性。

> [!NOTE]
> 具有多个节点类型的常见情景是包含前端服务和后端服务的应用程序。 要将前端服务放在端口向 Internet 开放的较小型 VM（D2_V2 等 VM 大小）上，同时要将后端服务放在没有向 Internet 开放端口的较大型 VM（D3_V2、D6_V2、D15_V2 等 VM 大小）上。
> 

1. 选择节点类型的名称（1 到 12 个字符，只能包含字母和数字）。
2. 主节点类型的 VM **大小**下限取决于为群集选择的**持久性**层。 持久性层的默认值为 bronze。 有关持久性的详细信息，请参阅[如何选择 Service Fabric 群集持久性][service-fabric-cluster-durability]。
3. 选择**虚拟机大小**。 D 系列 VM 具有 SSD 驱动器，强烈建议用于有状态应用程序。 不要使用任何具有部分核心或可用磁盘容量小于 10 GB 的 VM SKU。 如需选择 VM 大小的帮助，请参阅 [Service Fabric 群集规划注意事项文档][service-fabric-cluster-capacity]。
4.  **单节点群集和三节点群集**仅供用于测试。 它们不支持任何正在运行的生产工作负荷。
5. 为节点类型选择**初始 VM 规模集容量**。 稍后，可以在节点类型中扩大或缩小 Vm 的数量，但在主节点类型上，生产工作负荷的最小值为5。 其他节点类型可以具有最少一台 VM。 主节点类型的 VM **数目**下限决定了群集的**可靠性**。  
6. 配置**自定义终结点**。 可在此字段中输入以逗号分隔的端口列表，可以通过 Azure 负载均衡器针对应用程序向公共 Internet 公开这些端口。 例如，如果计划在群集中部署 Web 应用程序，请在此处输入“80”，允许端口 80 的流量进入群集。 有关终结点的详细信息，请参阅[与应用程序进行通信][service-fabric-connect-and-communicate-with-services]
7. **启用反向代理**。  借助 [Service Fabric 反向代理](service-fabric-reverseproxy.md)，Service Fabric 群集中运行的微服务可以发现包含 http 终结点的其他服务，并与之通信。
8. 返回“群集配置”  边栏选项卡，在“+显示可选设置”  下，配置群集**诊断**。 默认情况下，已在群集上启用诊断，以帮助排查问题。 若要禁用诊断，请将其“状态”  切换为“关”  。 **不**建议关闭诊断。 如果已创建 Application Insights 项目，则提供该项目密钥，以便向其路由应用程序跟踪。
9. **包括 DNS 服务**。  [DNS 服务](service-fabric-dnsservice.md)是一项可选服务，可用于查找使用 DNS 协议的其他服务。
10. 选择要将群集设置为的**结构升级模式**。 如果希望系统自动选取最新可用版本并尝试将群集升级到该最新版本，则选择“自动”  。 如果想要选择受支持的版本，则将模式设置为“手动”  。 有关 Fabric 升级模式的详细信息，请参阅 [Service Fabric 群集升级文档][service-fabric-cluster-upgrade]。

> [!NOTE]
> 我们仅支持那些运行受支持的 Service Fabric 版本的群集。 选择“手动”  模式，即表示由你负责将群集升级到受支持的版本。
> 

### <a name="3-security"></a>3.安全性
![Azure 门户上安全配置的屏幕截图。][BasicSecurityConfigs]

为轻松设置安全测试群集，我们提供了“基本”选项  。 如果已有证书且已将其上传到 [Key Vault](/azure/key-vault/)（并为部署启用了 Key Vault），则使用“自定义”选项 

#### <a name="basic-option"></a>“基本”选项
按照屏幕进行操作，以添加或重复使用现有 Key Vault 并添加证书。 添加证书是一个同步过程，因此必须等待证书创建完成。

在前面的过程完成前，请勿离开屏幕。

![CreateKeyVault]

现在，已创建了一个 Key Vault，可以编辑 Key Vault 的访问策略了。 

![CreateKeyVault2]

单击“编辑访问策略”并为部署启用对虚拟机的访问权限。   建议同时启用模板部署。 完成选择后，不要忘记单击“保存”按钮并关闭“访问策略”窗格   。

![CreateKeyVault3]

输入证书的名称并单击“确定”。 

![CreateKeyVault4]

#### <a name="custom-option"></a>“自定义”选项
如果你已执行“基本”选项中的步骤，请跳过本节内容  。

![SecurityCustomOption]

你需要源密钥保管库、证书 URL 和证书指纹信息以完成“安全性”页。 如果“安全性”页未就绪，请打开另一个浏览器窗口，然后在 Azure 门户中执行以下操作

1. 导航到密钥保管库服务。
2. 选择“属性”选项卡，并将“资源 ID”复制到另一个浏览器窗口上的“源密钥保管库” 

    ![CertInfo0]

3. 现在，选择“证书”选项卡。
4. 单击“证书指纹”，转到“版本”页。
5. 单击当前版本下显示的 GUID。

    ![CertInfo1]

6. 你现在应位于如下所示的屏幕上。 将十六进制 SHA-1 指纹复制到另一个浏览器窗口中的“证书指纹”
7. 将“机密标识符”复制到另一个浏览器窗口上的“证书 URL”。

    ![CertInfo2]

选中“**配置高级设置**”复选框，输入**管理客户端**和**只读客户端**的客户端证书。 在这些字段中，输入管理客户端证书的指纹和只读用户客户端证书的指纹（如果适用）。 当管理员尝试连接群集时，仅当他们的证书指纹与此处输入的指纹值匹配时，才被授予访问权限。  

### <a name="4-summary"></a>4.摘要

现在可开始部署群集了。 执行此操作前，请下载证书，查找较大蓝色信息框内的链接。 请确保将证书保存在安全的位置。 连接到群集时需要此证书。 由于下载的证书没有密码，因此建议添加一个。

若要完成群集创建，请单击“创建”  。 还可以选择性下载模板。

![摘要]

可以在通知栏中查看群集创建进度。 （单击屏幕右上角状态栏附近的铃铛图标）。如果在创建群集时曾经单击“固定到启动板”，则会看到“部署 Service Fabric 群集”已固定到“启动”板。    此过程将需要一些时间才能完成。 

若要使用 Powershell 或 CLI 对群集执行管理操作，需要连接群集，请参阅[连接群集](service-fabric-connect-to-secure-cluster.md)，了解有关如何连接的详细信息。

## <a name="view-your-cluster-status"></a>查看群集状态
![仪表板中群集详细信息的屏幕截图。][ClusterDashboard]

创建群集后，可以在门户检查群集：

1. 转到“浏览”  ，然后单击“Service Fabric 群集”  。
2. 找到该群集并单击它。
3. 现在，仪表板会显示群集的详细信息，包括群集的公共终结点和 Service Fabric Explorer 的链接。

群集仪表板边栏选项卡上的“**节点监视器**”部分显示运行正常和不正常的 VM 的数目。 若要了解有关群集运行状况的详细信息，请参阅 [Service Fabric 运行状况模型简介][service-fabric-health-introduction]。

> [!NOTE]
> Service Fabric 群集需要一定数量的节点始终处于开机状态，以维护可用性和保留状态（称为“维护仲裁”）。 因此，除非已事先执行[状态的完整备份][service-fabric-reliable-services-backup-restore]，否则关闭群集中的所有计算机通常是不安全的做法。
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>远程连接到虚拟机规模集实例或群集节点
每次在群集中指定 NodeTypes，都会设置虚拟机规模集。 <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>后续步骤
此时，已创建一个使用证书进行管理身份验证的安全群集。 接下来，请[连接到该群集](service-fabric-connect-to-secure-cluster.md)，了解如何[管理应用程序机密](service-fabric-application-secret-management.md)。  此外，了解 [Service Fabric 支持选项](service-fabric-support.md)。

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[摘要]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
