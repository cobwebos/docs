# <a name="securing-docker-containers-in-azure-container-service"></a>保护 Azure 容器服务中的 Docker 容器

本文介绍有关保护 Azure 容器服务中部署的 Docker 容器的注意事项和建议。 其中的许多注意事项一般也适用于 Azure 或其他环境中部署的 Docker 容器。 

## <a name="image-security"></a>映像安全性

容器是基于一个或多个存储库中存储的映像构建的。 这些存储库可能属于公共或专用容器注册表。 [Docker 中心](https://hub.docker.com/)就是公共注册表的一个例子。 [Docker 受信任注册表](https://docs.docker.com/datacenter/dtr/2.0/)是专用注册表的例子，它可以安装在本地或者安装在虚拟私有云中。 还有基于云的专用容器注册表服务，包括 [Azure 容器注册表](../articles/container-registry/container-registry-intro.md)。

### <a name="public-and-private-images"></a>公共和专用映像
一般情况下，与任何公开发布的软件包一样，公用容器映像不提供安全保证。 容器映像包括多个软件层，每个软件层可能有漏洞。 了解容器映像的来源至关重要，包括映像的所有者（确定源是否可靠）、映像包括的软件层和软件版本。 

例如，在 Docker Hub 中心转到官方 [nginx 存储库](https://hub.docker.com/_/nginx/)并导航到“标记”选项卡后，可以看到每个映像中带有颜色编码的漏洞。 每种颜色描绘了映像某个软件层的漏洞。 有关在 Docker 中心扫描漏洞的详细信息，请参阅[在 Docker 中心了解官方存储库](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/)。

![Docker 中心上的 Nginx 映像](./media/container-service-security/docker-hub-nginx.png)

企业极其关注安全性，为了抵御安全攻击，应该在 Azure 容器注册表或 Docker 受信任注册表等专用注册表中存储和检索映像。 除了提供托管的专用注册表以外，Azure 容器注册表还支持通过 Azure Active Directory 使用[基于服务主体的身份验证](../articles/container-registry/container-registry-authentication.md)执行基本身份验证流，包括使用只读、写入和所有者权限进行基于角色的访问。

### <a name="image-security-scanning"></a>映像安全扫描

即使使用专用注册表，也最好是使用映像扫描解决方案来增强安全验证。 容器映像中的每个软件层经常会出现容器映像中其他层所不存在的漏洞。 随着越来越多的公司开始基于容器技术部署生产工作负荷，映像扫描已变得非常重要，它可以确保在组织中防范安全威胁。 

可以使用 [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry)、[Aqua Security](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry) 等安全监视和扫描解决方案来扫描专用注册表中的容器映像并识别潜在漏洞。 必须了解不同解决方案提供的扫描深度。 例如，某些解决方案可能只针对已知漏洞交叉验证映像层。 这些解决方案可能无法验证通过某些包管理器软件构建的映像层软件。 其他解决方案集成了更深入的扫描功能，可以查找任何打包软件中的漏洞。

### <a name="production-deployment-rules-and-audit"></a>生产部署规则和审核
在生产环境中部署应用程序后，必须设置一些规则来确保生产环境中使用的映像安全且不包含任何漏洞。

* 一般而言，不应该允许在生产环境中运行包含漏洞的映像（即使是轻微的漏洞）。 此外，最好是将生产环境中部署的所有映像保存在只有少数人可以访问的专用注册表中。 另一个要点是，保存少量的生产映像，确保能够有效地对其进行管理。

* 由于公用容器映像中软件的来源很难查明，因此，最好是从源构建映像，确保能够识别层的来源。 自制容器映像中出现漏洞时，客户可以更快地找到解决途径。 使用公共映像时，客户需要找到公共映像的根才能修复漏洞，或者需要从发布者获取另一个安全映像。

* 在生产环境中部署的、经过全面扫描的映像并不能保证在应用程序的生存期内保持最新状态。 系统可能会针对映像的层报告以前并不知道的或者在生产部署后才引入的安全漏洞。 有必要定期审核生产环境中部署的映像，以识别过时的映像或有一段时间未更新的映像。 可以使用蓝绿部署方法和滚动升级机制来更新容器映像，这不会造成停机。 可以使用上一部分所述的工具执行映像扫描。 

* 用于构建映像及执行集成安全扫描的持续集成 (CI) 管道有助于保持专用注册表的安全和容器映像的安全。 CI 解决方案中内置的漏洞扫描可确保将通过所有测试的映像推送到从中部署了生产工作负荷的专用注册表中。 CI 管道故障可确保不会将有漏洞的映像推送到用于生产工作负荷部署的专用注册表中。 如果存在大量的映像，CI 管道还会自动执行映像安全扫描。 相比之下，在映像中手动审核安全漏洞的过程可能相当繁琐且容易出错。

## <a name="host-level-container-isolation"></a>主机级容器隔离
当客户在 Azure 资源中部署容器应用程序时，这些应用程序将部署在资源组中的订阅级别，并且不属于多租户应用程序。 这意味着，如果客户与其他人共享某个订阅，则无法在同一个订阅中的两个部署之间构建边界。 因此，无法保证容器级安全性。 

此外，必须知道，容器共享主机的内核和资源（即，Azure 容器服务包含群集中的 Azure VM）。 因此，在生产环境中运行的容器必须以非特权用户模式运行。 使用 root 特权运行容器可能会危害整个环境。 获得容器中的 root 级访问权限后，黑客便获得了主机上的完全 root 特权。 此外，必须使用只读的文件系统运行容器。 这可以防止有权访问已遭入侵容器的人员在文件系统中写入恶意脚本，获取对其他文件的访问权限。 同样，必须限制分配给容器的资源（例如内存、CPU 和网络带宽）。 这有助于防止黑客占用资源，实施信用卡欺诈或比特币挖掘等非法活动，从而导致其他容器无法在主机或群集中运行。

## <a name="orchestrator-considerations"></a>协调器注意事项

Azure 容器服务中提供的每个协调器具有其自身的安全注意事项。 例如，应该限制对容器服务中的协调器节点进行直接 SSH 访问。 而是应该使用每个协调器的 UI 或命令行工具（例如，适用于 Kubernetes 的`kubectl`），在不访问主机的情况下管理容器环境。 有关详细信息，请参阅[与 Kubernetes、DC/OS 或 Docker Swarm 群集建立远程连接](../articles/container-service/kubernetes/container-service-connect.md)。

有关特定于协调器的其他安全信息，请参阅以下资源：

* **Kubernetes**：[Kubernetes 部署的安全最佳做法](http://blog.kubernetes.io/2016/08/security-best-practices-kubernetes-deployment.html)

* **DC/OS**：[保护群集](https://dcos.io/docs/1.8/administration/securing-your-cluster/)

* **Docker Swarm**：[Docker 安全性](https://www.docker.com/docker-security)

## <a name="next-steps"></a>后续步骤

* 有关 Docker 体系结构和容器安全性的详细信息，请参阅[容器安全性简介](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf)。

* 有关 Azure 平台安全性的信息，请参阅 [Azure 安全中心](https://www.microsoft.com/en-us/trustcenter/cloudservices/azure)。