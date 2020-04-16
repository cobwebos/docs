---
title: X.509 服务结构群集中基于证书的身份验证
description: 了解 Service Fabric 群集中基于证书的身份验证，以及如何检测、缓解和修复与证书相关的问题。
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429663"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>X.509 服务结构群集中基于证书的身份验证

本文补充了[Service Fabric 群集安全性](service-fabric-cluster-security.md)的介绍，并详细介绍了 Service Fabric 群集中基于证书的身份验证的详细信息。 我们假设读者熟悉基本的安全概念，以及 Service Fabric 公开控制群集安全性的控件。  

此标题涵盖的主题：

* 基于证书的身份验证基础知识
* 身份及其各自角色
* 证书配置规则
* 故障排除和常见问题

## <a name="certificate-based-authentication-basics"></a>基于证书的身份验证基础知识
作为一个简短的复习，在安全性中，证书是一种工具，旨在将有关实体（主体）的信息绑定到他们拥有一对非对称加密密钥，因此构成了公钥加密的核心构造。 证书表示的密钥可用于保护数据或证明密钥持有者的身份;当与公钥基础结构 （PKI） 系统结合使用时，证书可以表示其主体的其他特征，例如互联网域的所有权或证书颁发者（称为证书颁发机构或 CA）授予它的某些特权。 证书的常见应用支持传输层安全 （TLS） 加密协议，允许通过计算机网络进行安全通信。 具体来说，客户端和服务器使用证书来确保其通信的隐私和完整性，并进行相互身份验证。

在 Service Fabric 中，群集（联合）的基本层也基于 TLS（以及其他协议）构建，以实现参与节点的可靠、安全网络。 通过 Service Fabric 客户端 API 连接到群集时，还可以使用 TLS 来保护流量，以及建立各方的身份。 具体而言，在 Service Fabric 中用于身份验证时，证书可用于证明以下声明：a） 证书凭据的演示者拥有证书的私钥 b）证书的 SHA-1 哈希（"指纹"）与群集定义中包含的声明匹配，或者 c） 证书的区分主题通用名称与群集定义中包含的声明匹配和证书的颁发者已知或受信任。

在上面的列表中，"b"被通俗地称为"指纹固定";在这种情况下，声明是指特定的证书，身份验证方案的强度取决于在计算上不可行的证书，该证书产生与另一个相同的哈希值，同时在所有其他方面仍然是一个有效、格式良好的对象。 项目"c"是宣布证书的替代方法，其中方案的强度取决于证书主体和颁发机构的组合。 在这种情况下，声明引用一类证书 - 任何具有相同特征的两个证书都被视为完全等效。 

以下各节将深入解释 Service Fabric 运行时如何使用和验证证书以确保群集安全性。

## <a name="identities-and-their-respective-roles"></a>身份及其各自角色
在深入了解身份验证或保护通信通道的详细信息之前，必须列出参与的参与者及其在群集中扮演的相应角色：
- Service Fabric 运行时，称为"系统"：提供表示群集的抽象和功能的一组服务。 当提到系统实例之间的群集内通信时，我们将使用术语"群集标识";当将群集称为群集外部流量的接收者/目标时，我们将使用术语"服务器标识"。
- 托管应用程序，称为"应用程序"：由群集所有者提供的代码，该代码在群集中进行编排和执行
- 客户端：允许根据群集配置连接到群集中并执行功能的实体。 我们分别区分两个级别的权限 - "用户"和"管理员"。 "用户"客户端主要限于只读操作（但不是所有只读功能），而"管理员"客户端可以不受限制地访问群集的功能。 （有关详细信息，请参阅[服务结构群集中的安全角色](service-fabric-cluster-security-roles.md)。
- （仅限 Azure）服务结构服务，用于协调并公开用于操作和管理服务结构群集的控件，称为"服务"。 根据环境，"服务"可能引用 Azure 服务结构资源提供程序或服务结构团队拥有和操作的其他资源提供程序。

在安全群集中，每个角色都可以配置它们自己的不同标识，声明为预定义角色名称及其相应凭据的配对。 Service Fabric 支持将凭据声明为证书或基于域的服务主体。 （支持基于 Windows/Kerberos 的标识，但超出了本文的范围;请参阅[服务结构群集中基于 Windows 的安全性](service-fabric-windows-cluster-windows-security.md)。在 Azure 群集中，客户端角色也可以声明为[基于 Azure 活动目录的标识](service-fabric-cluster-creation-setup-aad.md)。

如上所述，Service Fabric 运行时定义了群集中的两个特权级别："管理员"和"用户"。 管理员客户端和"系统"组件都将使用"管理员"权限运行，因此无法相互区分。 在群集中建立连接时，Service Fabric 运行时将授予经过身份验证的调用方作为后续授权的基点之一。 我们将在以下各节中深入检查身份验证。

## <a name="certificate-configuration-rules"></a>证书配置规则
### <a name="validation-rules"></a>验证规则
Service Fabric 群集的安全设置原则上描述了以下几个方面：
- 身份验证类型;这是群集的创建时间、不可变特性。 此类设置的示例包括"群集凭据类型"、"服务器凭据类型"，允许的值为"无"、"x509"或"窗口"。 本文重点介绍 x509 类型的身份验证。
- （身份验证）验证规则;这些设置由群集所有者设置，并描述给定角色应接受哪些凭据。 下面将深入研究示例。
- 用于调整或巧妙地更改身份验证结果的设置;此处的示例包括限制执行证书吊销列表的标志（取消）等。

> [!NOTE]
> 下面提供的群集配置示例是 XML 格式的群集清单的摘录，作为直接支持本文中描述的 Service Fabric 功能的最消化格式。 相同的设置可以直接在群集定义的 JSON 表示形式中表示，无论是独立的 json 群集清单还是 Azure 资源管理模板。

证书验证规则包含以下元素：
- 相应的角色：客户端、管理员客户端（特权角色）
- 为角色接受的凭据，通过指纹或主题通用名称声明

#### <a name="thumbprint-based-certificate-validation-declarations"></a>基于指纹的证书验证声明
对于基于指纹的验证规则，请求连接到群集的调用方提供的凭据将验证如下：
  - 凭据是一个有效的、格式良好的证书：可以构建其链，签名匹配
  - 证书是时间有效的（在<之前 = 现在<不是）
  - 证书的 SHA-1 哈希与声明匹配，作为不区分大小写的字符串比较，不包括所有空格

链构建或验证期间遇到的任何信任错误都将针对基于指纹的声明进行抑制，但过期证书除外，尽管该案例也确实存在规定。 具体来说，与：吊销状态未知或脱机、不受信任的根、无效的密钥使用、部分链相关的失败被视为非致命错误;在这种情况下，前提是证书只是密钥对的信封 - 安全性在于群集所有者已设置位置度量值来保护私钥。

以下来自群集清单的摘录体现了这样一组基于指纹的验证规则：

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

上述每个条目都引用前面描述的特定标识;每个条目还允许指定多个值，作为逗号分隔的字符串列表。 在此示例中，在成功验证传入凭据后，带有 SHA-1 指纹的证书的演示者"d5ec...4264'将被授予"管理员"角色;相反，使用证书"7c8f...01b0 将被授予"用户"角色，仅限于只读操作。 显示指纹为"abcd...1234" 或"ef01"5678' 将作为群集中的对等节点被接受。 最后，连接到群集的管理终结点的客户端将期望服务器证书的指纹为"ef01...5678'. 

如前所述，Service Fabric 确实为接受过期证书提供了规定;原因是证书的生存期有限，并且，当通过指纹声明（指特定的证书实例）时，允许证书过期将导致无法连接到群集，或群集完全崩溃。 很容易忘记或忽略旋转指纹固定证书，不幸的是，从这种情况恢复是困难的。

为此，群集所有者可以明确说明，由指纹声明的自签名证书应视为有效，如下所示：

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
此行为不扩展到 CA 颁发的证书;因此，此行为不会扩展到 CA 颁发的证书。如果是这样的话，一个被吊销的、已知待泄露的过期证书，只要它不再出现在 CA 的证书吊销列表中，就会变得"有效"，从而带来安全风险。 使用自签名证书时，群集所有者被视为负责保护证书私钥的唯一一方，而 CA 颁发的证书则不是这种情况 - 群集所有者可能不知道其证书被声明如何或何时被宣布泄露。

#### <a name="common-name-based-certificate-validation-declarations"></a>常见的基于名称的证书验证声明
基于名称的常见声明采用以下形式之一：
- 主题通用名称（仅）
- 主题通用名称与颁发者固定

让我们首先考虑一个类清单的摘录，该清单体现了两种声明样式：
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
声明分别引用服务器和群集标识;请注意，基于 CN 的声明在群集清单中有自己的部分，与标准"安全性"分开。 在这两个声明中，"Name"表示证书的可区分主题通用名称，而"价值"字段表示预期的颁发者，如下所示：

- 在第一种情况下，声明指出服务器证书的可分辨主题的通用名称元素应与字符串"server.demo.system.system.servicefabric.azure-int"匹配;空"值"字段表示期望证书链的根在正在验证服务器证书的节点/计算机上受信任;在 Windows 上，这意味着证书可以链接到"受信任的根 CA"存储中安装的任何证书;
- 在第二种情况下，声明指出，如果证书的通用名称与字符串"cluster.demo.system.system.servicefabric.azure-int"匹配，则证书的演示者作为群集中的对等节点被接受，*并且*证书的直接颁发者与"值"字段中的逗号分隔条目之一匹配。 （此规则类型被通俗地称为"具有颁发者固定的通用名称"。

在这两种情况下，证书的链都是构建的，并且预期没有错误;即，吊销错误、部分链或时间无效信任错误被视为致命错误，并且证书验证将失败。 固定颁发者将导致将"不受信任的根"状态视为非致命错误;尽管出现，这是一种更严格的验证形式，因为它允许群集所有者将授权/接受的颁发器集约束到自己的 PKI。

构建证书链后，根据标准 TLS/SSL 策略验证它，声明的主题为远程名称;如果证书的主题通用名称或其任何主题替代名称与群集清单中的 CN 声明匹配，则证书将被视为匹配项。 在这种情况下，支持通配符，并且字符串匹配不区分大小写。

（我们应该明确，上述序列可以针对证书声明的每种类型的密钥用法执行;如果证书指定客户端身份验证密钥用法，则首先为客户端角色构建并计算链。 如果成功，评估完成并验证成功。 如果证书没有客户端身份验证用法，或者验证失败，则 Service Fabric 运行时将生成和评估服务器身份验证链。

为了完成该示例，以下摘录说明了按通用名称声明客户端证书：
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

上述声明分别对应于管理员和用户标识;以这种方式声明的证书的验证与前面的示例群集和服务器证书的描述完全一致。

> [!NOTE]
> 基于名称的常见声明旨在简化群集证书的轮换，以及一般管理群集证书。 但是，建议遵守以下建议，以确保群集的持续可用性和安全性：
  * 更喜欢颁发者固定而不是依赖受信任的根
  * 避免混合来自不同 PKI 的发行人
  * 确保所有预期的发行人都列在证书声明中;不匹配的颁发者将导致验证失败
  * 确保 PKI 的证书策略终结点是可发现、可用和可访问的 - 这意味着 AIA、CRL 或 OCSP 终结点在叶证书上声明，并且它们可以访问，以便证书链构建可以完成。

将所有这些内容捆绑在一起，在收到使用 X.509 证书保护的群集中的连接请求时，Service Fabric 运行时将使用群集的安全设置来验证上述远程方的凭据;如果成功，则调用方/远程方被视为经过身份验证。 如果凭据与多个验证规则匹配，运行时将授予调用方任何匹配规则的最高特权角色。 

### <a name="presentation-rules"></a>演示规则
上一节介绍身份验证在证书保护群集中的工作原理;本节将解释 Service Fabric 运行时本身如何发现和加载它用于群集内通信的证书;我们称这些为"演示"规则。

与验证规则一样，表示规则指定角色和关联的凭据声明，这些声明由指纹或通用名称表示。 与验证规则不同，基于名称的通用声明没有用于颁发者固定的规定;这提供了更大的灵活性，以及更高的性能。 表示规则在群集清单的"NodeType"部分（s）中声明，每个不同的节点类型;设置从群集的安全部分拆分，以允许每个节点类型在单个部分中具有其完整配置。 在 Azure 服务结构群集中，节点类型证书声明默认为群集定义的安全部分中的相应设置。

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>基于指纹的证书表示声明
如前所述，Service Fabric 运行时区分其作为群集中其他节点的对等体的角色和群集管理操作的服务器。 原则上，这些设置可以明显配置，但实际上它们倾向于对齐。 对于本文的其余部分，我们将假定设置匹配，以便简单。

让我们考虑以下群集清单的摘录：
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
"群集证书"元素演示完整架构，包括可选参数（"X509FindValue"）或具有适当默认值的参数（"X509StoreName"）;其他声明显示缩写形式。 上面的群集证书声明指出，类型为"nt1vm"的节点的安全设置使用证书"cc71"进行初始化。1984年'为主，和'49e2.。19d6' 证书作为辅助证书;这两个证书都应在本地计算机\'My 的证书存储（或 Linux 等效路径 *、var/lib/sfcerts）* 中找到。

#### <a name="common-name-based-certificate-presentation-declarations"></a>常见的基于名称的证书表示声明
节点类型证书也可以按主题通用名称声明，如下图所示：

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

对于任一类型的声明，Service Fabric 节点将在启动时读取配置，查找并加载指定的证书，并按其 NotAfter 属性的降序排序它们;将忽略过期的证书，并且选择列表的第一个元素作为此节点尝试的任何 Service Fabric 连接的客户端凭据。 （实际上，Service Fabric 倾向于最远的过期证书。

请注意，对于基于通用名称的表示声明，如果证书的主题通用名称等于声明的 X509FindValue（或 X509FindValue）字段，则证书被视为匹配项，作为区分大小写的、精确的字符串比较。 这与验证规则（它确实支持通配符匹配）以及不区分大小写的字符串比较形成鲜明对比。  

### <a name="miscellaneous-certificate-configuration-settings"></a>其他证书配置设置
前面提到过，Service Fabric 群集的安全设置还允许巧妙地更改身份验证代码的行为。 虽然有关[Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)的文章表示全面和最新的设置列表，但我们将在此处扩展某些安全设置的含义，以完成基于证书的身份验证的完整公开。 对于每个设置，我们将解释意图、默认值/行为、它如何影响身份验证以及哪些值是可以接受的。

如前所述，证书验证始终意味着构建和评估证书的链。 对于 CA 颁发的证书，这种看似简单的 OS API 调用通常需要对颁发 PKI 的各个终结点进行多个出站调用、缓存响应等。 鉴于 Service Fabric 群集中证书验证调用的流行程度，PKI 终结点中的任何问题都可能导致群集的可用性降低或彻底崩溃。 虽然无法禁止外出呼叫（请参阅下面的常见问题部分，了解有关此内容的更多内容），但以下设置可用于屏蔽 CRL 调用失败引起的验证错误。

  * CrlCheckingFlag - 在"安全"部分下，字符串转换为 UINT。 Service Fabric 使用此设置的值通过更改链构建的行为来屏蔽证书链状态错误;它作为"dwFlags"参数传递到 Win32 CryptoAPI [CertGet证书链](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain)调用，并可以设置为函数接受的任何有效标志组合。 值 0 强制 Service Fabric 运行时忽略任何信任状态错误 - 不建议这样做，因为它的使用将构成重大的安全暴露。 默认值为 0x4000000（CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT）。

  使用时间：对于本地测试，具有未完全形成/没有适当的公钥基础结构来支持证书的自签名证书或开发人员证书。 在 PKI 之间的过渡期间，也可以在空气间隙环境中用作缓解。

  如何使用：我们将举一个示例，强制吊销检查以仅访问缓存的 URL。 假设：
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  然后群集清单中的声明变为：
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * 忽略CrlOfflineError - 在"安全"部分下，默认值为"false"的布尔。 表示禁止"脱机吊销"链构建错误状态（或后续链策略验证错误状态）的快捷方式。

  何时使用：本地测试，或没有适当的 PKI 支持的开发人员证书。 在空气间隙环境中或已知无法访问 PKI 时用作缓解措施。

  如何使用：
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  其他值得注意的设置（全部在"安全"部分下）：
  * 接受过期固定群集证书 - 在专用于基于指纹的证书验证部分中讨论;允许接受过期的自签名群集证书。 
  * 证书到期安全保证金 - 间隔，在证书的"非过期时间戳"之前几分钟表示，在此期间，证书被视为有过期风险。 Service Fabric 监视群集证书，并定期发布有关其剩余可用性的运行状况报告。 在"安全"间隔内，这些运行状况报告将提升为"警告"状态。 默认值为 30 天。
  * 证书运行状况报告间隔 - 控制有关群集证书剩余时间有效性的运行状况报告的频率。 报告将仅在此间隔发出一次。 该值以秒为单位表示，默认值为 8 小时。
  * 强制预验证安全更改 - 布尔，在检测到安全设置更改时控制群集升级的行为。 如果设置为"true"，群集升级将尝试确保至少一个与任何表示规则匹配的证书可以传递相应的验证规则。 在将新设置应用于任何节点之前执行预验证，但在启动升级时仅在承载群集管理器服务主副本的节点上运行。 在撰写本文时，该设置的默认值为"false"，并且对于以 7.1 开头的运行时版本的新 Azure 服务结构群集，该设置将设置为"true"。
 
### <a name="end-to-end-scenario-examples"></a>端到端方案（示例）
我们已经查看了表示规则、验证规则和调整标志，但这一切如何协同工作？ 在本节中，我们将介绍两个端到端示例，演示如何利用安全设置进行安全群集升级。 请注意，这不是关于 Service Fabric 中正确证书管理的全面论文，要查找有关该主题的配套文章。

列报规则和验证规则的分离提出了一个明显的问题（或担心），即这些规则能否产生分歧，以及后果如何。 事实上，节点选择的身份验证证书可能无法通过另一个节点的验证规则。 事实上，这种差异是导致与身份验证相关的事件的主要原因。 同时，这些规则的分离允许群集在升级期间继续运行，从而更改群集的安全设置。 请考虑，首先将验证规则作为第一步进行增强，群集的所有节点都将在使用当前凭据时收敛到新设置上。 

回想一下，在 Service Fabric 群集中，升级通过（最多 5 个）"升级域"或"U"进行。 仅在给定时间升级/更改当前 UD 中的节点，并且只有在群集的可用性允许时，升级才会继续到下一个 UD。 （有关更多详细信息，请参阅[服务结构群集升级](service-fabric-cluster-upgrade.md)和其他有关同一主题的文章。证书/安全更改的风险特别大，因为它们可以将节点与群集隔离，或使群集处于仲裁丢失的边缘。

我们将使用以下表示法来描述节点的安全设置：

Nk： [P：[TP]A]，V：[TP]A]，其中：
  - "Nk"表示升级域*k*中的节点
  - "P"表示节点的当前表示规则（假设我们仅指群集证书）; 
  - "V"表示节点的当前验证规则（仅限群集证书）
  - "TP_A"表示基于指纹的声明 （TP），其中"A"表示证书指纹
  - "CN_B"表示基于名称的通用声明 （CN），其中"B"是证书的主题通用名称 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>旋转由指纹声明的群集证书
以下序列描述如何使用 2 阶段升级安全地引入辅助群集证书（由指纹声明）;第一阶段在验证规则中引入了新的证书声明，第二阶段在表示规则中引入了它：
  - 初始状态：N0 = [P：[TP]A}，V：[TP]A}，...Nk = [P：[TP]A}，V：[TP]A}- 群集处于静止状态，所有节点共享通用配置
  - 完成升级域 0： N0 = [P：[TP]A]，V：[TP_A，TP=B]，...Nk = [P：TP]A、V：[TP]A}- UD0 中的节点将呈现证书 A，并接受证书 A 或 B;所有其他节点仅存在并接受证书 A
  - 完成上次升级域时：N0 = [P：[TP]A}，V：[TP_A，TP=B]，...Nk = [P：TP]A、V：[TP_A，TP_B]- 所有存在证书 A 的节点，所有节点都将接受证书 A 或 B
      
此时，群集再次处于平衡状态，升级/更改安全设置的第二阶段可以开始：
  - 完成升级域 0： N0 = [P：TP_A， TP=B]， V：[TP_A， TP=B]， ...Nk = [P：TP]A、V：[TP_A，TP_B]- UD0 中的节点将开始呈现 B，群集中的任何其他节点都接受 B。
  - 完成上次升级域时：N0 = [P：[TP]A，TP=B=，V：[TP_A，TP=B]，...Nk = {P：[TP}A，TP=B]，V：[TP]A，TP=B}- 所有节点都已切换到证书 B。 证书 A 现在可以通过一组后续升级从群集定义中停用/删除。

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>将群集从指纹转换为基于通用名称的证书声明
同样，更改证书声明的类型（从指纹到通用名称）将遵循与上述相同的模式。 请注意，验证规则允许通过指纹和同一群集定义中的通用名称声明给定角色的证书。 相比之下，表示规则只允许一种形式的声明。 顺便说一句，将群集证书从指纹转换为通用名称的安全方法是首先通过指纹引入预期的目标证书，然后将该声明更改为基于名称的通用证书。 在下面的示例中，我们将假设指纹"A"和主题通用名称"B"引用同一证书。 

  - 初始状态：N0 = [P：[TP]A}，V：[TP]A}，...Nk = [P：[TP]A、V：[TP]A}- 群集处于静止状态，所有节点共享通用配置，A 是主证书指纹
  - 完成升级域 0： N0 = [P：[TP]A]，V：[TP_A，CN=B]，...Nk = [P：TP]A、V：[TP]A}- UD0 中的节点将呈现证书 A，并接受指纹 A 或通用名称 B 的证书;所有其他节点仅存在并接受证书 A
  - 完成上次升级域时：N0 = [P：[TP]A}，V：[TP_A，CN=B]，...Nk = [P：TP]A、V：[TP_A，CN_B]- 所有节点都存在证书 A，所有节点将接受证书 A （TP） 或 B （CN）

此时，我们可以通过后续升级继续更改表示规则：
  - 完成升级域 0： N0 = [P：CN]B]，V：[TP_A，CN=B]，...Nk = [P：TP_A]， V：[TP_A， CN_B] - UD0 中的节点将呈现 CN 找到的证书 B，并接受带有指纹 A 或通用名称 B 的证书;所有其他节点都存在并接受证书 A，由指纹选择
  - 完成上次升级域时：N0 = [P：CN]B}，V：[TP_A，CN=B]，...Nk = [P：CN]B、V：[TP_A，CN]- 所有节点都提供 CN 找到的证书 B，所有节点将接受证书 A （TP） 或 B （CN）
    
第 2 阶段完成的还标志着群集转换为基于名称的通用证书;在后续的群集升级中，可以删除基于指纹的验证声明。

> [!NOTE]
> 在 Azure 服务结构群集中，上面显示的工作流由服务结构资源提供程序协调;在 Azure 服务结构结构群集中，上述工作流由服务结构资源提供程序协调。群集所有者仍负责根据指示的规则（表示或验证）将证书预配到群集中，并鼓励他们执行多个步骤的更改。

在单独的文章中，我们将讨论将证书管理和预配到 Service Fabric 群集中的主题。

## <a name="troubleshooting-and-frequently-asked-questions"></a>故障排除和常见问题
虽然在 Service Fabric 群集中调试与身份验证相关的问题并不容易，但我们希望以下提示和提示会有所帮助。 开始调查的最简单方法是检查群集节点上的 Service Fabric 事件日志 - 不仅一定显示症状，而且检查已启动但无法连接到其邻居之一的节点。 在 Windows 上，重要的事件通常分别记录在"应用程序和服务日志_Microsoft-ServiceFabric_Admin"或"操作"通道下。 有时，[启用 CAPI2 日志记录](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)可能会有所帮助，以获取有关证书验证、CRL/CTL 等检索的更多详细信息（请记住在完成重新专业后禁用它，它可以相当详细。

在遇到身份验证问题的群集中表现出的典型症状包括： 
  - 节点已关闭/循环 
  - 连接尝试被拒绝
  - 连接尝试超时

每个症状可能由不同的问题引起，并且相同的根本原因可能表现出不同的表现;因此，我们将只列出一个典型问题的一个小样本，并推荐它们来修复它们。 

* 节点可以交换消息，但不能连接。 连接尝试终止的可能原因是"证书不匹配"错误 - Service Fabric 到 Service Fabric 连接中的一方正在提交证书，该证书未通过收件人的验证规则。 可能伴随着以下任一错误： 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  要进一步诊断/调查：在尝试连接的每个节点上，确定正在显示的证书;检查证书并尝试模拟验证规则（检查指纹或通用名称相等性，如果指定，请检查颁发者指纹）。

  另一个常见的伴随错误代码可能是：
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  在这种情况下，证书按通用名称声明，以下任一项适用：
    - 颁发者未固定，并且根证书不受信任，或者
    - 颁发者被固定，但声明不包括此证书的直接颁发者的经验

* 节点已打开，但无法连接到其他节点;其他节点不会从故障节点接收入站流量。 在这种情况下，证书加载可能在本地节点上失败。 查找以下错误：
  - 找不到证书 - 确保表示规则中声明的证书可以通过 LocalMachine_My（或指定）证书存储的内容解析。 
    故障的可能原因可能包括： 
      - 指纹声明中的无效字符
      - 未安装证书
      - 证书已过期
      - 通用名称声明包括前缀"CN+"
      - 声明指定通配符，证书存储中不存在完全匹配项（声明：CN_.mydomain.com，实际证书：CN_server.mydomain.com）

  - 未知凭据 - 指示与证书对应的缺少私钥，通常附带错误代码： 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    要进行补救，检查私钥的存在;验证 SFAdmins 被授予对私钥的"读取"访问权限。

  - 错误提供程序类型 - 指示加密新一代 （CNG） 证书（"微软软件密钥存储提供程序"）;此时，服务交换矩阵仅支持 CAPI1 证书。 通常附带错误代码：
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    若要进行补救，请使用 CAPI1（例如"Microsoft 增强 RSA 和 AES 加密提供程序"）提供程序重新创建群集证书。 有关加密提供程序的更多详细信息，请参阅[了解加密提供程序](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

