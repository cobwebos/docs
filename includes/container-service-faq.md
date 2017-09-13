# <a name="container-service-frequently-asked-questions"></a>容器服务常见问题

## <a name="orchestrators"></a>协调器

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>在 Azure 容器服务上支持哪些容器协调器？ 

支持开源 DC/OS、Docker Swarm 和 Kubernetes。 有关详细信息，请参阅[概述](../articles/container-service/kubernetes/container-service-intro-kubernetes.md)。
 
### <a name="do-you-support-docker-swarm-mode"></a>是否支持 Docker Swarm 模式？ 

Swarm 模式目前不受支持，但已列入服务计划。 

### <a name="does-azure-container-service-support-windows-containers"></a>Azure 容器服务是否支持 Windows 容器？  

目前 Linux 容器在所有协调器中受支持。 对 Kubernetes 的 Windows 容器的支持处于预览状态。

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>是否建议在 Azure 容器服务中使用特定的协调器？ 
通常情况下，我们不提供特定协调器方面的建议。 如果体验过某个受支持的协调器，可以将该体验应用在 Azure 容器服务中。 不过，数据趋势表明，DC/OS 已在生产环境中证明适用于大数据和 IoT 工作负荷，Kubernetes 适用于云原生工作负荷，Docker Swarm 已知可以集成 Docker 工具且易于学习。

用户也可以使用其他 Azure 服务，根据自己的情况生成和管理自定义容器解决方案。 这些服务包括[虚拟机](../articles/virtual-machines/linux/overview.md)、[Service Fabric](../articles/service-fabric/service-fabric-overview.md)、[Web 应用](../articles/app-service-web/app-service-web-overview.md)和[批处理](../articles/batch/batch-technical-overview.md)。  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Azure 容器服务和 ACS 引擎的区别是什么？ 
Azure 容器服务是支持 SLA 的 Azure 服务，具有 Azure 门户、Azure 命令行工具和 Azure API 中提供的功能。 用户可以使用该服务快速实现和管理运行标准容器协调工具的群集，配置选项相对较少。 

[ACS 引擎](http://github.com/Azure/acs-engine)是一个开源项目，适用于高级用户在所有级别自定义群集配置。 可以更改基础结构和软件的配置意味着，我们不提供针对 ACS 引擎的 SLA。 支持问题是通过 GitHub 上的开源项目处理的，不通过官方的 Microsoft 渠道。 

如需其他详细信息，请参阅[适用于容器的支持策略](https://support.microsoft.com/en-us/help/4035670/support-policy-for-containers)。

## <a name="cluster-management"></a>群集管理

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>如何为群集创建 SSH 密钥？

可以使用操作系统上的标准工具创建一个 SSH RSA 公钥和私钥对，以便针对群集的 Linux 虚拟机进行身份验证。 有关步骤，请参阅 [OS X 和 Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) 或 [Windows](../articles/virtual-machines/linux/ssh-from-windows.md) 指南。 

如果使用 [Azure CLI 2.0 命令](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md)部署容器服务群集，则可为群集自动生成 SSH 密钥。

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>如何为 Kubernetes 群集创建服务主体？

若要在 Azure 容器服务中创建 Kubernetes 群集，还需要 Azure Active Directory 服务主体 ID 和密码。 有关详细信息，请参阅[关于 Kubernetes 群集的服务主体](../articles/container-service/kubernetes/container-service-kubernetes-service-principal.md)。

如果使用 [Azure CLI 2.0命令](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md)部署 Kubernetes 群集，则可为群集自动生成服务主体凭据。

### <a name="how-large-a-cluster-can-i-create"></a>可以创建多大的群集？
可以创建包含 1、3 或 5 个主节点的群集。 最多可以选择 100 个代理节点。

> [!IMPORTANT]
> 对于更大的群集，根据你为节点选择的 VM 大小，可能需要增加订阅中的内核配额。 若要请求增加配额，可免费建立[联机客户支持请求](../articles/azure-supportability/how-to-create-azure-support-request.md)。 如果使用的是 [Azure 免费帐户](https://azure.microsoft.com/free/)，则仅可以使用有限数量的 Azure 计算核心。
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>创建群集后，如何提高主机数？ 
创建群集后，主机数是固定的，不能更改。 在创建群集的过程中，理想情况下应选择多个主机以确保高可用性。

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>创建群集后，如何提高代理数？ 
可以使用 Azure 门户或命令行工具缩放群集中的代理数。 请参阅[缩放 Azure 容器服务群集](../articles/container-service/kubernetes/container-service-scale.md)。

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>我的主机和代理的 URL 是什么？ 
Azure 容器服务中群集资源的 URL 基于所提供的 DNS 名称前缀以及为部署选择的 Azure 区域的名称。 例如，主节点的完全限定域名 (FQDN) 采用以下形式：

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

可以在 Azure 门户、Azure 资源浏览器或其他 Azure 工具中找到群集的常用 URL。

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>如何确定在群集中运行的 Orchestrator 版本？

* DC/OS：请参阅 [Mesosphere 文档](https://support.mesosphere.com/hc/en-us/articles/207719793-How-to-get-the-DCOS-version-from-the-command-line-)
* Docker Swarm：运行 `docker version`
* Kubernetes：运行 `kubectl version`

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>如何在部署后升级 Orchestrator？

目前，Azure 容器服务不提供可将部署到群集的 Orchestrator 版本升级的工具。 如果容器服务支持更高的版本，可以部署新群集。 另一选项是使用特定于 Orchestrator 的工具（如果该工具可以用来对群集就地升级）。 有关示例，请参阅 [DC/OS 升级](https://dcos.io/docs/1.8/administration/upgrading/)。
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>在哪里查找用于连接到我的群集的 SSH 连接字符串？

可以在 Azure 门户中查找连接字符串，也可以通过 Azure 命令行工具查找。 

1. 在门户中，导航到群集部署的资源组。  

2. 单击“概览”，并在“概要”下单击“部署”的链接。 

3. 在“部署历史记录”边栏选项卡中，单击其名称以 **microsoft-acs** 开头（后跟部署日期）的部署。 示例：microsoft-acs-201701310000。  

4. 在“摘要”页的“输出”下通过了多个群集链接。 **SSHMaster0** 提供的 SSH 连接字符串用于连接到容器服务群集中的第一个主机。 

如上文所述，也可使用 Azure 工具查找主机的 FQDN。 使用主机的 FQDN 以及在创建群集时指定的用户名建立到主机的 SSH 连接。 例如：

```bash
ssh userName@masterFQDN –A –p 22 
```

有关详细信息，请参阅[连接到 Azure 容器服务群集](../articles/container-service/kubernetes/container-service-connect.md)。

## <a name="next-steps"></a>后续步骤

* [详细了解](../articles/container-service/kubernetes/container-service-intro-kubernetes.md) Azure 容器服务。
* 使用[门户](../articles/container-service/dcos-swarm/container-service-deployment.md)或 [Azure CLI 2.0](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) 部署容器服务群集。
