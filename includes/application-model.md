# <a name="compute"></a>计算
利用 Azure，可以部署和监视在 Microsoft 数据中心内运行的应用程序代码。 在 Azure 上创建应用程序并运行它时，相关代码和配置统称为 Azure 托管服务。 使用新版应用程序代码，可以轻松管理、缩放、重新配置以及更新托管服务。 本文重点介绍 Azure 托管服务应用程序模型。<a id="compare" name="compare"></a>

## 目录<a id="_GoBack" name="_GoBack"></a>
* [Azure 应用程序模型的优势][Azure Application Model Benefits]
* [托管服务的核心概念][Hosted Service Core Concepts]
* [托管服务设计注意事项][Hosted Service Design Considerations]
* [设计应用程序以进行缩放][Designing your Application for Scale]
* [托管服务的定义和配置][Hosted Service Definition and Configuration]
* [服务定义文件][The Service Definition File]
* [服务配置文件][The Service Configuration File]
* [创建和部署托管服务][Creating and Deploying a Hosted Service]
* [参考][References]

## <a id="benefits"> </a>Azure 应用程序模型的优势
将应用程序作为托管服务部署时，Azure 会创建一个或多个包含应用程序代码的虚拟机 (VM)，并在驻留在其中一个 Azure 数据中心中的物理计算机上启动 VM。 在客户端对托管应用程序的请求进入数据中心时，负载均衡器会将这些请求平均分发给 VM。 当应用程序在 Azure 中托管时，它会获得三个关键优势：

* **高可用性。** 高可用性意味着 Azure 确保应用程序尽可能地长时间运行并能够响应客户端请求。 如果应用程序终止（例如由于未经处理的异常），Azure 会检测到这种情况，并自动重新启动应用程序。 如果应用程序在其中运行的计算机遇到某种硬件故障，Azure 也会检测到这种情况，并自动在另一台正常工作的物理计算机上创建新 VM 并从该位置运行用户的代码。 注意：为使应用程序获得 Microsoft 99.95% 可用的服务级别协议，必须至少使用两个 VM 来运行应用程序代码。 这样，在 Azure 将代码从发生故障的 VM 移到新的可用 VM 时，该 VM 就可以处理客户端请求。
* **可伸缩性。** 在 Azure 中，可以轻松动态更改运行应用程序代码的 VM 数量来处理放在应用程序上的实际负载。 这允许根据客户置于应用程序上的工作负载来调整应用程序，同时仅在需要时为所需 VM 付费。 需要更改 VM 数量时，Azure 可在几分钟内做出响应，根据需要随时动态更改运行中的 VM 数量。
* **可管理性。** 由于 Azure 是平台即服务 (PaaS) 产品，所以它管理使这些计算机能够持续运行所需的基础结构（硬件本身、电力和网络）。 Azure 还管理平台，确保最新操作系统具有所有正确的修补程序和安全更新以及组件更新，例如 .NET Framework 和 Internet Information Server。 由于所有 VM 都运行 Windows Server 2008，因此 Azure 提供诸如诊断监视、远程桌面支持、防火墙和证书存储配置之类的附加功能。 提供的所有这些功能都无需支付额外费用。 事实上，在 Azure 中运行应用程序时，Windows Server 2008 操作系统 (OS) 许可证已包括在内。 由于所有 VM 都运行 Windows Server 2008，因此 Windows Server 2008 上运行的任何代码在 Azure 中运行时均运行良好。

## <a id="concepts"> </a>托管服务的核心概念
将应用程序作为托管服务部署到 Azure 中时，它将作为一个或多个*角色*运行。 简单来说，*角色*是指应用程序文件和配置。 可以为应用程序定义一个或多个角色，其中每个角色都具有自己的一组应用程序文件和配置。 对于应用程序中的每个角色，可以指定要运行的 VM（即*角色实例*）的数量。 下图显示了使用角色和角色实例建模为托管服务的应用程序的两个简单示例。

##### <a name="figure-1-a-single-role-with-three-instances-vms-running-in-an-azure-data-center"></a>图 1：单个角色，包含 Azure 数据中心内运行的三个实例 (VM)
![图像][0]

##### <a name="figure-2-two-roles-each-with-two-instances-vms-running-in-an-azure-data-center"></a>图 2：两个角色，每个角色包含 Azure 数据中心内运行的两个实例 (VM)
![图像][1]

角色实例通常处理通过*输入终结点*进入数据中心的 Internet 客户端请求。 一个角色可以拥有 0 个或多个输入终结点。 每个终结点指示一种协议（HTTP、HTTPS 或 TCP）和一个端口。 通常将角色配置为拥有两个输入终结点：在端口 80 上侦听的 HTTP 和在端口 443 上侦听的 HTTPS。 下图显示了使用不同的输入终结点将客户端请求定向到自己的两个不同角色的示例。

![图像][2]

在 Azure 中创建托管服务时，会为它分配可公开寻址的 IP 地址，客户端可通过该 IP 地址访问托管服务。 在创建托管服务时，还必须选择映射到该 IP 地址的 URL 前缀。 此前缀必须是唯一的，因为实际上会保留 *前缀*.cloudapp.net URL，以便其他任何人都无法拥有它。 客户端使用该 URL 与角色实例通信。 通常，我们不会分发或发布 Azure *前缀*.cloudapp.net URL， 而是从所选的 DNS 注册机构购买 DNS 名称，将 DNS 名称配置为将客户端请求重定向到 Azure URL。 有关更多详细信息，请参阅[在 Azure 中配置自定义域名][Configuring a Custom Domain Name in Azure]。

## <a id="considerations"> </a>托管服务设计注意事项
将应用程序设计为在云环境中运行时，应考虑几个注意事项，例如延迟、高可用性和伸缩性。

在 Azure 中运行托管服务时，决定应用程序代码的位置是一个重要考虑事项。 通常将应用程序部署到距离客户端最近的数据中心，以减少延迟并获得尽可能最佳的性能。
不过，如果对数据及其驻留位置存在一些司法或法律方面的顾虑，则可以选择距离公司或数据较近的数据中心。 全球有六个数据中心能够托管应用程序代码。 下表显示了可用位置：

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<tbody>

<tr>

<td style="width: 100px;">
**国家/地区**

</td>

<td style="width: 200px;">
**子区域**

</td>
</tr>

<tr>

<td>
美国

</td>

<td>
中南部和中北部

</td>
</tr>

<tr>

<td>
欧洲

</td>

<td>
北部和西部

</td>
</tr>

<tr>

<td>
亚洲

</td>

<td>
东南部和东部

</td>
</tr>
</tbody>
</table>
在创建托管服务时，需要选择一个子区域，以指示希望在其中执行代码的位置。

若要实现高可用性和可伸缩性，应用程序数据应保存在可供多个角色实例访问的中央存储库中，这一点至关重要。 为帮助实现此目的，Azure 提供了多个存储选项，例如 Blob、表和 SQL 数据库。 有关这些存储技术的详细信息，请参阅 [Azure 中的数据存储产品/服务][Data Storage Offerings in Azure]一文。 下图显示了 Azure 数据中心内的负载均衡器如何将客户端请求分发给不同的角色实例，所有这些角色实例都可以访问同一数据存储。

![图像][3]

通常，你希望让应用程序代码和数据置于同一数据中心中，因为这样可以在应用程序代码访问数据时实现低延迟（更高的性能）。 另外，在同一数据中心内移动数据时，无需为带宽付费。

## <a id="scale"> </a>设计应用程序以进行缩放
有时，我们可能希望采用单个应用程序（例如简单的网站）并将它托管在 Azure 中。 但应用程序可能经常包括多个全都协同工作的角色。 例如，在下图中，“网站”角色有两个实例，“订单处理”角色有三个实例，“报告生成器”角色有一个实例。 这些角色全都协同工作，并且所有这些角色的代码可以打包在一起并作为单个单元部署到 Azure 中。

![图像][4]

将应用程序拆分为各自在自己的一组角色实例（即 VM）上运行的不同角色的主要原因是为了独立缩放角色。 例如，在节假日期间，可能有许多客户从公司购买产品，因此可能需要增加运行“网站”角色的角色实例的数量以及运行“订单处理”角色的角色实例的数量。 在节假日之后，可能会收得大量退回的产品，因此可能仍需要大量“网站”实例，但需要的“订单处理”实例数减少。 在一年的其余时间，可能只需要少数几个“网站”和“订单处理”实例。 在所有这些时段中，可能只需一个 Report Generator 实例。 Azure 中基于角色的部署的灵活性使用户能够根据业务需要轻松调整应用程序。

让托管服务中的角色实例相互通信是一种很常见的做法。 例如，“网站”角色接受客户的订单，但随后它将订单处理的负载分流给“订单处理”角色实例。 将工作从一组角色实例传递给另一组实例的最佳方法是使用 Azure 提供的队列技术 - 队列服务或服务总线队列。 队列的使用是此处所述情形的关键部分。 队列使托管服务能够独立缩放其角色，从而允许根据成本平衡工作负载。 如果队列中的消息数随时间增加，则可以增加 Order Processing 角色实例的数量。 如果队列中的消息数随时间减少，则可以减少 Order Processing 角色实例的数量。 这样就只需为处理实际工作负载所需的实例付费。

队列还提供了可靠性。 在减少“订单处理”角色实例的数量时，Azure 会决定终止哪些实例。 它可能决定终止正在处理队列消息的实例。 不过，因为消息处理未成功完成，消息再次变得对获取并处理它的另一个 Order Processing 角色实例可见。 由于队列消息的可见性，可以保证消息最终会得到处理。 队列还通过有效地将消息分发给从队列请求消息的所有角色实例来充当负载均衡器。

对于“网站”角色实例，可以监视进入它们的流量，并决定是增加还是减少它们的数量。 队列允许独立于“订单处理”角色实例来缩放“网站”角色实例的数量。 这非常强大，并且提供了极好的灵活性。 当然，如果应用程序包括其他角色，可以添加其他队列作为它们之间通信的管道，以利用相同的缩放和成本优势。

## <a id="defandcfg"> </a>托管服务的定义和配置
要将托管服务部署到 Azure，还需要提供服务定义文件和服务配置文件。 这两个文件都是 XML 文件，它们允许以声明方式为托管服务指定部署选项。 服务定义文件描述构成托管服务的所有角色以及它们如何通信。 服务配置文件描述每个角色的实例数以及用于配置每个角色实例的设置。

## <a id="def"> </a>服务定义文件
正如我在前面提到的，服务定义 (CSDEF) 文件是一个 XML 文件，描述构成完整应用程序的各种角色。 可以从以下位置找到该 XML 文件的完整架构：[http://msdn.microsoft.com/library/windowsazure/ee758711.aspx][]。
CSDEF 文件包含应用程序中所需的每个角色的 WebRole 或 WorkerRole 元素。 将角色作为 Web 角色部署（使用 WebRole 元素）意味着代码会在包含 Windows Server 2008 和 Internet Information Server (IIS) 的角色实例上运行。
将角色作为辅助角色部署（使用 WorkerRole 元素）意味着角色实例上将具有 Windows Server 2008（不会安装 IIS）。

当然，可以创建并部署使用某种其他机制侦听传入 Web 请求的辅助角色（例如，代码可以创建并使用 .NET HttpListener）。 由于角色实例全都运行 Windows Server 2008，因此代码可以执行 Windows Server 上运行的应用程序通常可以执行的任何操作。
2008.

对于每个角色，需要指示该角色的实例应使用的适当 VM 大小。 下表显示了如今可用的各种 VM 大小以及每种大小的特性：

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<tbody>

<tr align="left" valign="top">

<td>
**VM 大小**

</td>

<td>
**CPU**

</td>

<td>
**RAM**

</td>

<td>
**磁盘**

</td>

<td>
**网络 I/O 峰值**

</td>
</tr>

<tr align="left" valign="top">

<td>
**特小型**

</td>

<td>
1 x 1.0 GHz

</td>

<td>
768 MB

</td>

<td>
20GB

</td>

<td>
\~5 Mbps

</td>
</tr>

<tr align="left" valign="top">

<td>
**小型**

</td>

<td>
1 x 1.6 GHz

</td>

<td>
1.75 GB

</td>

<td>
225GB

</td>

<td>
\~100 Mbps

</td>
</tr>

<tr align="left" valign="top">

<td>
**中型**

</td>

<td>
2 x 1.6 GHz

</td>

<td>
3.5 GB

</td>

<td>
490GB

</td>

<td>
\~200 Mbps

</td>
</tr>

<tr align="left" valign="top">

<td>
**大型**

</td>

<td>
4 x 1.6 GHz

</td>

<td>
7 GB

</td>

<td>
1TB

</td>

<td>
\~400 Mbps

</td>
</tr>

<tr align="left" valign="top">

<td>
**特大型**

</td>

<td>
8 x 1.6 GHz

</td>

<td>
14 GB

</td>

<td>
2 TB

</td>

<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
按小时对用作角色实例的每个 VM 收费，并且还对角色实例向数据中心以外发送的任何数据收费。 不会对进入数据中心的数据收费。 有关详细详细，请参阅 [Azure 定价][Azure 定价]。 一般来说，建议使用许多小角色实例而非少数大实例，以便应用程序具有更强的故障恢复能力。 毕竟，拥有的角色实例越少，其中某个实例中的故障对整个应用程序造成的灾难就越大。 另外，正如前面提到的，必须为每个角色至少部署两个实例，才能获得 Microsoft 提供的 99.95% 的服务级别协议。

还应该在服务定义 (CSDEF) 文件中指定有关应用程序中每个角色的许多特性。 下面是可供使用的一些较有用的项：

* **证书**。 如果要加密数据或者希望 Web 服务支持 SSL，则需要使用证书。 所有证书均需要上传到 Azure。 有关详细信息，请参阅[在 Azure 中管理证书][Managing Certificates in Azure]。 此 XML 设置以前上传的证书安装到角色实例的证书存储中，以便它们可供应用程序代码使用。
* **配置设置名称**。 针对希望应用程序在角色实例上运行时读取的值。 配置设置的实际值在服务配置 (CSCFG) 文件中设置，该文件可随时更新，而无需你重新部署代码。 事实上，可以通过这种方式对应用程序进行编码，以检测更改的配置值而不会引发任何停机。
* **输入终结点**。 在这里，指定要通过 *前缀*.cloadapp.net URL 向外界公开的任何 HTTP、HTTPS 或 TCP 终结点（包括端口）。 当 Azure 部署角色时，会自动在该角色实例上配置防火墙。
* **内部终结点**。 在这里指定要向作为应用程序一部分部署的其他角色实例公开的任何 HTTP 或 TCP 终结点。 内部终结点允许应用程序中的所有角色实例相互通信，但应用程序外部的任何角色实例均无法访问它们。
* **导入模块**。 这些模块在角色实例上有选择地安装有用组件。 存在用于诊断监视、远程桌面和 Azure Connect（允许角色实例通过安全通道访问本地资源）的组件。
* **本地存储**。 该项在角色实例上分配一个子目录以供应用程序使用。 [Azure 中的数据存储产品][Data Storage Offerings in Azure]一文中对此进行了更详细的介绍。
* **启动任务**。 利用启动任务，可以在角色实例启动时在它上面安装必备组件。 如果需要，可以将用户权限提升为管理员来运行任务。

## <a id="cfg"> </a>服务配置文件
服务配置 (CSCFG) 文件是一个 XML 文件，描述无需重新部署应用程序即可更改的设置。 可以从以下位置找到该 XML 文件的完整架构：[http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]。
CSCFG 文件包含应用程序中每个角色的 Role 元素。 下面是可以在 CSCFG 文件中指定的一些项：

* **OS 版本**。 此属性允许选择用于运行应用程序代码的所有角色实例的操作系统 (OS) 版本。 此 OS 称为*来宾 OS*，并且每个新版本都包括来宾 OS 发布时可用的最新安全修补程序和更新。 如果将 osVersion 属性值设置为“\*”，Azure 将在新来宾 OS 版本可用时自动更新每个角色实例上的来宾 OS。 不过，可以通过选择特定来宾 OS 版本来取消自动更新。 例如，将 osVersion 属性设置为值“WA-GUEST-OS-2.8\_201109-01”会使所有角色实例获取以下网页上说明的内容：[http://msdn.microsoft.com/library/hh560567.aspx][http://msdn.microsoft.com/library/hh560567.aspx]。 有关来宾 OS 版本的详细信息，请参阅[管理 Azure 来宾 OS 的升级]。
* **实例**。 此元素的值指示要设置的角色实例数量，这些实例将运行特定角色的代码。 由于可以将新的 CSCFG 文件上传到 Azure（无需重新部署应用程序），因此更改此元素的值并上传新的 CSCFG 文件来动态增加或减少运行应用程序代码的角色实例数非常简单。 这样，就知道如何轻松地缩放应用程序来满足实际工作负载需求，同时还能控制因为运行角色实例而支付的费用数额。
* **配置设置值**。 此元素指示 CSDEF 文件中定义的设置的值。 角色可以在运行时读取这些值。 这些配置设置值通常用于 SQL 数据库或 Azure 存储的连接字符串，但它们可用于所需的任何目的。

## <a id="hostedservices"> </a>创建和部署托管服务
创建托管服务需要首先转到 [Azure 管理门户]，并通过指定 DNS 前缀和最终希望代码在其中运行的数据中心来预配托管服务。 然后在开发环境中，创建服务定义 (CSDEF) 文件，构建应用程序代码并将所有这些文件打包（压缩）到服务包 (CSPKG) 文件中。 还必须准备服务配置 (CSCFG) 文件。 为了部署角色，需要使用 Azure 服务管理 API 上传 CSPKG 和 CSCFG 文件。 部署后，Azure 会在数据中心设置角色实例（基于配置数据），从包中提取应用程序代码，将它复制到角色实例，然后启动实例。 现在，代码已经可以正常运行。

下图显示了在开发计算机上创建的 CSPKG 和 CSCFG 文件。 CSPKG 文件包含 CSDEF 文件和两个角色的代码。 使用 Azure 服务管理 API 上传 CSPKG 和 CSCFG 文件后，Azure 会在数据中心创建角色实例。 在此示例中，CSCFG 文件指示 Azure 应创建角色 \#1 的三个实例和角色 \#2 的两个实例。

![图像][5]

有关部署、升级和重新配置角色的详细信息，请参阅[部署和更新 Azure 应用程序][Deploying and Updating Azure Applications]一文。<a id="Ref" name="Ref"></a>

## <a id="references"> </a>参考
* [为 Azure 创建托管服务][Creating a Hosted Service for Azure]
* [在 Azure 中管理托管服务][Managing Hosted Services in Azure]
* [将应用程序迁移到 Azure][Migrating Applications to Azure]
* [配置 Azure 应用程序][Configuring an Azure Application]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>作者：Jeffrey Richter (Wintellect)</p>

</div>

[Azure Application Model Benefits]: #benefits
[Hosted Service Core Concepts]: #concepts
[Hosted Service Design Considerations]: #considerations
[Designing your Application for Scale]: #scale
[Hosted Service Definition and Configuration]: #defandcfg
[The Service Definition File]: #def
[The Service Configuration File]: #cfg
[Creating and Deploying a Hosted Service]: #hostedservices
[References]: #references
[0]: ./media/application-model/application-model-3.jpg
[1]: ./media/application-model/application-model-4.jpg
[2]: ./media/application-model/application-model-5.jpg
[Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
[Data Storage Offerings in Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
[3]: ./media/application-model/application-model-6.jpg
[4]: ./media/application-model/application-model-7.jpg

[Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
[Managing Certificates in Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
[http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
[管理 Azure 来宾 OS 的升级]: http://msdn.microsoft.com/library/ee924680.aspx
[Azure 管理门户]: http://manage.windowsazure.com/
[5]: ./media/application-model/application-model-8.jpg
[Deploying and Updating Azure Applications]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
[Creating a Hosted Service for Azure]: http://msdn.microsoft.com/library/gg432967.aspx
[Managing Hosted Services in Azure]: http://msdn.microsoft.com/library/gg433038.aspx
[Migrating Applications to Azure]: http://msdn.microsoft.com/library/gg186051.aspx
[Configuring an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
