---
title: X.509 在 Service Fabric 群集中基于证书的身份验证
description: 了解 Service Fabric 群集中基于证书的身份验证，以及如何检测、缓解和修复与证书相关的问题。
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429663"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>Service Fabric 群集中基于证书的 x.509 身份验证

本文补充了[Service Fabric 群集安全](service-fabric-cluster-security.md)的简介，并详细介绍了 Service Fabric 群集中基于证书的身份验证。 假设读者熟悉基本的安全概念，以及 Service Fabric 公开来控制群集安全性的控件。  

本标题下面涵盖的主题：

* 基于证书的身份验证基础知识
* 标识及其各自的角色
* 证书配置规则
* 故障排除和常见问题

## <a name="certificate-based-authentication-basics"></a>基于证书的身份验证基础知识
作为安全方面的一项简短复习，证书是一种用于将有关实体（使用者）的信息绑定到其拥有一对非对称加密密钥的乐器，因此构成了公钥加密的核心构造。 证书所表示的密钥可用于保护数据，或用于证明密钥持有者的身份;当与公钥基础结构（PKI）系统一起使用时，证书可以表示其使用者的其他特征（如 internet 域的所有权）或证书颁发机构向其授予的特定权限（称为证书颁发机构或 CA）。 证书的常见应用程序支持传输层安全（TLS）加密协议，允许通过计算机网络进行安全的通信。 具体而言，客户端和服务器使用证书来确保其通信的隐私和完整性，并同时执行相互身份验证。

在 Service Fabric 中，群集（联合）的基本层还基于 TLS （其他协议）建立，以实现一种可靠、安全的参与节点网络。 通过 Service Fabric 客户端 Api 连接到群集时，还会使用 TLS 来保护流量，并同时建立参与方的标识。 具体而言，在 Service Fabric 中使用身份验证时，证书可用于证明以下声明： a）证书凭据的表示器已拥有证书的私钥 b）证书的 SHA-1 哈希（"指纹"）与群集定义中包含的声明匹配，或 c）证书的可分辨使用者公用名与群集定义中包含的声明匹配和证书的颁发者是已知的或可信的。

在上面的列表中，"b" 俗称称为 "指纹固定";在这种情况下，声明指的是特定的证书和身份验证方案的强度，其计算不可行是伪造一个证书，该证书生成与另一个相同的哈希值，而在所有其他方面仍是有效的格式正确的对象。 项 "c" 表示声明证书的替代形式，其中方案的强度在证书的使用者和颁发机构的组合上。 在这种情况下，声明引用一类证书-具有相同特征的任意两个证书被视为完全等效。 

以下部分将详细说明 Service Fabric 运行时如何使用和验证证书，以确保群集安全。

## <a name="identities-and-their-respective-roles"></a>标识及其各自的角色
在深入了解身份验证或保护通信通道的详细信息之前，请务必列出参与参与者及其在群集中扮演的相应角色：
- Service Fabric 运行时（称为 "system"）：提供表示群集的抽象和功能的服务集。 当指的是系统实例之间的群集内通信时，我们将使用术语 "群集标识";在将群集作为来自群集外部的流量的接收方/目标引用时，我们将使用术语 "服务器标识"。
- 托管应用程序称为 "应用程序"：群集所有者提供的代码，在群集中进行协调和执行
- 客户端：根据群集配置，允许在群集中连接和执行功能的实体。 我们分别区分两种级别的权限： "user" 和 "admin"。 "用户" 客户端主要限制为只读操作（但并非所有只读功能），而 "管理员" 客户端可以对群集的功能进行不受限制的访问。 （有关详细信息，请参阅[Service Fabric 群集中的安全角色](service-fabric-cluster-security-roles.md)。）
- （仅限 Azure） Service Fabric 用于协调和公开 Service Fabric 群集的操作和管理的控制的服务，称为 "服务"。 根据环境的不同，"服务" 可能指 Azure Service Fabric 资源提供程序或 Service Fabric 团队拥有和操作的其他资源提供程序。

在安全群集中，其中每个角色都可以使用其自己的不同标识进行配置，声明为预定义角色名称和相应凭据的配对。 Service Fabric 支持将凭据声明为证书或基于域的服务主体。 （Windows/Kerberos-based 标识也受支持，但超出了本文的范围; 请参阅[Service Fabric 群集中基于 Windows 的安全](service-fabric-windows-cluster-windows-security.md)。）在 Azure 群集中，还可以将客户端角色声明为[基于 Azure Active Directory 的标识](service-fabric-cluster-creation-setup-aad.md)。

与上面的提到一样，Service Fabric 运行时在群集中定义了两个级别的权限： "admin" 和 "user"。 管理员客户端和 "系统" 组件均可使用 "管理" 权限运行，因此彼此 undistinguishable。 建立与群集的连接后，Service Fabric 运行时将授予经过身份验证的调用方作为后续授权的基础。 在以下部分中，我们将深入探讨身份验证。

## <a name="certificate-configuration-rules"></a>证书配置规则
### <a name="validation-rules"></a>验证规则
Service Fabric 群集的安全设置描述了以下方面：
- 身份验证类型;这是群集的创建时不可变的特性。 此类设置的示例包括 "ClusterCredentialType" 和 "ServerCredentialType"，允许的值为 "none"、"x509" 或 "windows"。 本文重点介绍 x509 类型身份验证。
- （身份验证）验证规则;这些设置由群集所有者设置，并描述应接受给定角色的哪些凭据。 下面将深入说明示例。
- 用于调整或细微改变身份验证结果的设置;此处的示例包括标记（取消）限制证书吊销列表的强制执行。

> [!NOTE]
> 下面提供的群集配置示例是 XML 格式的群集清单中的摘录，它是摘要的最格式，它直接支持本文中所述的 Service Fabric 功能。 相同的设置可以直接在群集定义的 JSON 表示形式中表示，无论是独立的 json 群集清单还是 Azure 资源管理模板。

证书验证规则由以下元素组成：
- 对应的角色：客户端、管理员客户端（特权角色）
- 角色接受的凭据，按指纹或使用者公用名声明

#### <a name="thumbprint-based-certificate-validation-declarations"></a>基于指纹的证书验证声明
对于基于指纹的验证规则，请求传入/向群集中的连接的调用方提供的凭据将按如下方式进行验证：
  - 该凭据是格式正确的有效证书：可以生成其链、签名匹配
  - 证书有效时间（NotBefore <= now < NotAfter）
  - 证书的 SHA-1 哈希匹配声明，这是不区分大小写的字符串比较，不包括所有空格

对于基于指纹的声明，在链生成或验证过程中遇到的任何信任错误，除了过期的证书外，还会取消这些错误，不过，在这种情况下，也有一些规定。 具体而言，与相关的失败：吊销状态为未知或脱机、不受信任的根、密钥用法无效、部分链被视为非致命错误;在这种情况下，前提是该证书只是一种用于密钥对的信封，这是因为群集所有者已在 "位置" 度量值中设置了安全密钥。

群集清单中的以下摘录演示了一组基于指纹的验证规则：

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

上述每个条目引用了前面所述的特定标识;每个条目还允许以逗号分隔的字符串列表的形式指定多个值。 在此示例中，在成功验证传入凭据后，具有 SHA-1 指纹的证书的表示者为 5ec .。。4264 "将授予" 管理员 "角色;相反，调用方使用证书 "7c8f ..." 进行身份验证01b0 "将被授予" 用户 "角色，并且仅限于主要的只读操作。 表示证书的入站调用方，其指纹为 "abcd .。。1234 ' 或 ' ef015678将接受到群集中的对等节点。 最后，连接到群集的管理终结点的客户端将需要服务器证书的指纹为 "ef01"。5678 "。 

如前文所述，Service Fabric 确实允许接受过期的证书;原因是证书的生存期有限，并在指纹（指特定证书实例）声明时，允许证书过期将导致无法连接到群集，或者完全折叠了群集的情况。 很容易忘记或忽视旋转指纹固定的证书，遗憾的是，这种情况的恢复很困难。

为此，群集所有者可以显式声明由指纹声明的自签名证书应视为有效证书，如下所示：

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
此行为不会扩展到 CA 颁发的证书;如果是这种情况，则已吊销的、被盗用的已知证书过期证书将在 CA 的证书吊销列表中不再是有效的，并且会带来安全风险。 对于自签名证书，群集所有者被视为负责保护证书私钥的唯一一方，这种情况并不是 CA 颁发的证书，因此，群集所有者可能不知道其证书被声明为如何或何时被泄露。

#### <a name="common-name-based-certificate-validation-declarations"></a>基于公共名称的证书验证声明
常见的基于名称的声明采用以下形式之一：
- 使用者公用名（仅限）
- 带有颁发者固定的使用者公用名

首先，让我们从 exemplifying 这两种声明样式的群集清单中摘录摘录：
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
声明分别引用服务器和群集标识;请注意，基于 CN 的声明在群集清单中具有其自己的部分，不同于标准的 "安全"。 在这两个声明中，"Name" 表示证书的可分辨使用者公用名，"Value" 字段表示预期的颁发者，如下所示：

- 在第一种情况下，声明声明服务器证书的可分辨使用者的公用名称元素与字符串 "servicefabric" 相匹配，但空的 "Value" 字段表示在验证服务器证书的节点/计算机上信任证书链的根的假定条件;在 Windows 上，这意味着证书可以链接到 "受信任的根 CA" 存储区中安装的任何证书;
- 在第二种情况下，声明表明，如果证书的公用名与字符串 "servicefabric" 相匹配，则接受证书的表示器作为群集中的对等节点，证书的直接颁发*者的指纹*与 "值" 字段中的逗号分隔条目之一匹配。 （此规则类型俗称称为 "带有颁发者固定的公用名"。）

无论是哪种情况，都将生成证书的链，并将其错误地释放;也就是说，吊销错误、部分链或时间无效的信任错误被视为严重错误，证书验证将失败。 固定颁发者将导致将 "不受信任的根" 状态视为非致命错误;尽管有外观，但这是一种更严格的验证形式，因为它允许群集所有者将授权/接受的颁发者集限制为其自己的 PKI。

构建证书链后，将使用已声明的使用者作为远程名称的标准 TLS/SSL 策略来验证该证书链。如果证书的使用者公用名或它的任何使用者可选名称与群集清单中的 CN 声明相匹配，则会将该证书视为匹配项。 在这种情况下支持通配符，字符串匹配不区分大小写。

（我们应该阐明上述顺序可针对证书声明的每种类型的密钥使用情况执行; 如果证书指定了客户端身份验证密钥用法，则会首先为客户端角色生成并评估该链。 如果成功，评估完成并且验证成功。 如果证书未使用客户端身份验证，或者验证失败，则 Service Fabric 运行时将生成并评估服务器身份验证的链。）

若要完成此示例，以下摘录演示了如何按公用名声明客户端证书：
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

上面的声明分别对应于管理员和用户标识;以这种方式声明的证书的验证方式完全如群集和服务器证书的前面示例所述。

> [!NOTE]
> 常见的基于名称的声明旨在简化对群集证书的管理。 但是，建议遵循以下建议，以确保群集的持续可用性和安全性：
  * 首选颁发者固定以依赖受信任的根
  * 避免混合来自不同 Pki 的颁发者
  * 确保证书声明中列出了所有预期的颁发者;不匹配的颁发者将导致验证失败
  * 确保 PKI 的证书策略终结点是可发现的、可用和可访问的，这意味着 AIA、CRL 或 OCSP 终结点在叶证书上声明，并可访问这些终结点，以便证书链生成可以完成。

将其全部结合起来，在使用 x.509 证书保护的群集中收到连接请求时，Service Fabric 运行时将使用群集的安全设置来验证远程方的凭据，如上所述：如果成功，则将调用方/远程方视为已进行身份验证。 如果凭据与多个验证规则匹配，则运行时将向调用方授予任何匹配规则的最高特权角色。 

### <a name="presentation-rules"></a>演示规则
上一部分介绍了身份验证在证书保护的群集中的工作方式;本部分将说明 Service Fabric 运行时本身如何发现和加载用于群集中通信的证书;我们称之为 "演示" 规则。

与验证规则一样，表示规则按指纹或公用名指定角色和关联的凭据声明。 与验证规则不同，基于常用名称的声明没有用于固定颁发者的条款;这样可以提高灵活性并改善性能。 对于每个不同的节点类型，在群集清单的 "NodeType" 部分中声明了表示规则;这些设置会从群集的安全部分进行拆分，以允许每个节点类型在单个部分中具有其完整配置。 在 Azure Service Fabric 群集中，节点类型证书声明默认为其在群集定义的 "安全" 部分中的相应设置。

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>基于指纹的证书表示声明
如前文所述，Service Fabric 运行时将其角色区别于群集中其他节点的对等方，并将作为群集管理操作的服务器。 原则上，这些设置可以很明显地进行配置，但在实践中，它们往往会对齐。 对于本文的其余部分，我们将假定设置匹配以便于简单。

让我们从群集清单中考虑以下摘录：
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
"对 clustercertificate" 元素演示完整的架构，包括可选参数（"X509FindValueSecondary"）或具有适当默认值的参数（"X509StoreName"）;其他声明显示缩写形式。 上述群集证书声明规定，类型为 "nt1vm" 的节点的安全设置是用证书 "cc71." 初始化的。1984 "作为主副本，" 49e2 "。19d6 ' 证书作为辅助证书;这两个证书都应在 "LocalMachine\'my" 证书存储（或 Linux 等效路径， *var/lib/sfcerts*）中找到。

#### <a name="common-name-based-certificate-presentation-declarations"></a>基于公共名称的证书表示声明
还可以通过使用者公用名声明节点类型证书，如下所示：举例说明：

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

对于这两种类型的声明，Service Fabric 节点将在启动时读取配置，查找并加载指定的证书，并按 NotAfter 属性的降序对它们进行排序;将忽略过期证书，并将列表中的第一个元素选择为此节点尝试的任何 Service Fabric 连接的客户端凭据。 （实际上，Service Fabric 优先于最远的过期证书。）

请注意，对于基于常用名称的表示法声明，如果证书的使用者公用名等于声明的 X509FindValue （或 X509FindValueSecondary）字段，则将其视为匹配，精确的字符串比较。 这与验证规则相反，验证规则支持通配符匹配以及不区分大小写的字符串比较。  

### <a name="miscellaneous-certificate-configuration-settings"></a>其他证书配置设置
前面曾提到过，Service Fabric 群集的安全设置还允许对身份验证代码的行为进行细微的更改。 尽管[Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)的文章表示最新的设置列表，但我们将在此处选择一些安全设置，以完成基于证书的身份验证的完整公开。 对于每个设置，我们将介绍意向、默认值/行为、它如何影响身份验证以及哪些值是可接受的。

如前所述，证书验证始终意味着生成和评估证书链。 对于 CA 颁发的证书，这个明显简单的操作系统 API 调用通常需要对颁发 PKI 的各个终结点的多个出站调用，以及缓存响应等。 由于证书验证调用在 Service Fabric 群集中的传播，PKI 终结点中的任何问题都可能导致群集的可用性下降或完全细分。 尽管无法禁止出站调用（有关详细信息，请参阅下面的 "常见问题" 部分），但可以使用以下设置来掩盖由于 CRL 调用失败而导致的验证错误。

  * CrlCheckingFlag-在 "安全性" 部分下，将字符串转换为 UINT。 Service Fabric 使用此设置的值通过更改链生成的行为来掩盖证书链状态错误;它作为 "dwFlags" 参数传递到 Win32 CryptoAPI [CertGetCertificateChain](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain)调用中，并可设置为该函数接受的任何标志的有效组合。 值0强制 Service Fabric 运行时忽略任何信任状态错误，不建议这样做，因为它的使用会导致重大的安全风险。 默认值为0x40000000 （CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT）。

  何时使用：对于本地测试，使用自签名证书或开发人员证书，这些证书不是完整的，也不具有正确的公钥基础结构来支持证书。 在 Pki 之间过渡期间，还可以在有气流的环境中使用作为缓解措施。

  如何使用：我们将采用强制执行吊销检查来仅访问缓存的 Url 的示例。 担任
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  然后，群集清单中的声明将变为：
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError-在 "Security" 部分下，使用默认值 "false" 的布尔值。 表示用于取消 "吊销脱机" 链生成错误状态（或后续链策略验证错误状态）的快捷方式。

  何时使用：本地测试，或使用不受正确 PKI 支持的开发人员证书。 在有气流的环境中或在已知无法访问 PKI 时使用作为缓解措施。

  如何使用：
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  其他值得注意的设置（在 "安全性" 部分下）：
  * AcceptExpiredPinnedClusterCertificate-在专用于基于指纹的证书验证部分中讨论;允许接受过期的自签名群集证书。 
  * CertificateExpirySafetyMargin 时间间隔，以分钟为单位，以分钟为单位，以分钟为单位，以分钟为单位，在这段时间内证书将被视为有风险。 Service Fabric 监视群集证书，并在其剩余可用性上定期发出运行状况报告。 在 "安全" 间隔内，这些运行状况报告将提升为 "警告" 状态。 默认值为 30 天。
  * CertificateHealthReportingInterval-控制运行状况报告与群集证书的剩余时间有效性有关的频率。 报表只会按此间隔发出一次。 该值以秒为单位表示，默认值为8小时。
  * EnforcePrevalidationOnSecurityChanges-布尔值，控制在检测到安全设置的更改时群集升级的行为。 如果设置为 "true"，则群集升级将尝试确保至少一个匹配任何表示规则的证书可以传递相应的验证规则。 在将新设置应用于任何节点之前执行预验证，但仅在启动升级时承载群集管理器服务主要副本的节点上运行。 在撰写本文时，该设置的默认值为 "false"，并将设置为 "true"，以便为运行运行时版本7.1 的新 Azure Service Fabric 群集设置为 "true"。
 
### <a name="end-to-end-scenario-examples"></a>端到端方案（示例）
我们已经了解了演示规则、验证规则和调整标志，但这一切如何协同工作呢？ 在本部分中，我们将完成两个端到端示例，演示如何使用安全设置来进行安全群集升级。 请注意，这并不是 Service Fabric 中的正确证书管理的综合性 dissertation，请参阅该主题的相关文章。

与显示和验证规则分离时，无论它们是否可以分离，都有一个明显的问题（或问题）。 的确，节点的身份验证证书选择不会传递其他节点的验证规则。 事实上，这种差异是与身份验证相关的事件的主要原因。 同时，这些规则的隔离允许群集在升级期间继续运行，这会更改群集的安全设置。 请考虑：通过首先扩充验证规则作为第一步，所有群集节点都将在新的设置上聚合，同时仍使用当前凭据。 

回忆一下，在 Service Fabric 群集中，升级过程将会（最多5个）的 "升级域" 或 UDs 进行。 在给定的时间仅升级/更改当前 UD 中的节点，并且仅当群集的可用性允许后，升级才会继续到下一 UD。 （有关详细信息，请参阅同一主题中的[Service Fabric 群集升级](service-fabric-cluster-upgrade.md)和其他文章。）证书/安全更改特别有风险，因为它们可以将节点与群集隔离开来，或使群集处于仲裁丢失的边缘。

我们将使用以下表示法来描述节点的安全设置：

Nk： {P:{TP = A}，V:{TP = A}}，其中：
  - "Nk" 代表升级域*k*中的节点
  - "P" 表示节点的当前表示规则（假设只引用群集证书）; 
  - "V" 表示节点的当前验证规则（仅限群集证书）
  - "TP = A" 表示基于指纹的声明（TP），其中 "A" 是证书指纹
  - "CN = B" 表示基于名称的公共声明（CN），其 "B" 是证书的使用者公用名 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>旋转指纹声明的群集证书
以下顺序描述了如何使用2阶段升级来安全地引入由指纹声明的辅助群集证书;第一阶段在验证规则中引入了新的证书声明，第二阶段在演示规则中引入了新的证书声明：
  - 初始状态： N0 = {P:{TP = A}，V:{TP = A}}，.。。Nk = {P:{TP = A}，V:{TP = A}}-群集处于静止状态，所有节点共享一个通用配置
  - 完成升级域0： N0 = {P:{TP = A}，V:{TP = A，TP = B}}，.。。Nk = {P:{TP = A}，V:{TP = A}}-UD0 中的节点将显示证书 A，并接受证书 A 或 B;所有其他节点均存在并仅接受证书 A
  - 完成最后一个升级域时： N0 = {P:{TP = A}，V:{TP = A，TP = B}}，.。。Nk = {P:{TP = A}，V:{TP = A，TP = B}}-所有节点均显示证书 A，所有节点都接受证书 A 或 B
      
此时，群集再次在平衡中，升级/更改安全设置的第二个阶段可以：
  - 完成升级域0： N0 = {P:{TP = A，TP = B}，V:{TP = A，TP = B}}，.。。Nk = {P:{TP = A}，V:{TP = A，TP = B}}-UD0 中的节点将开始显示 B，群集中的任何其他节点都可以接受该节点。
  - 完成最后一个升级域时： N0 = {P:{TP = A，TP = B}，V:{TP = A，TP = B}}，.。。Nk = {P:{TP = A，TP = B}，V:{TP = A，TP = B}}-所有节点都已切换为显示证书 B。现在，可以从群集定义中停用或删除证书 A，并提供后续升级集。

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>将群集从指纹转换为基于常见名称的证书声明
同样，将证书声明类型（从指纹更改为公用名）将遵循上述相同的模式。 请注意，验证规则允许在同一群集定义中通过指纹和公用名声明给定角色的证书。 相反，表示规则只允许一种形式的声明。 顺便说一下，将群集证书从指纹转换为公用名称的安全方法是先按指纹引入目标证书，然后将该声明更改为基于公共名称的证书。 在下面的示例中，我们假定指纹 "A" 和使用者公用名 "B" 引用同一个证书。 

  - 初始状态： N0 = {P:{TP = A}，V:{TP = A}}，.。。Nk = {P:{TP = A}，V:{TP = A}}-群集处于静止状态，所有节点共享一个通用配置，并且是主证书指纹
  - 完成升级后，域0： N0 = {P:{TP = A}，V:{TP = A，CN = B}}，.。。Nk = {P:{TP = A}，V:{TP = A}}-UD0 中的节点将显示证书 A，并接受带有指纹 A 或公用名 B 的证书;所有其他节点均存在并仅接受证书 A
  - 完成最后一个升级域时： N0 = {P:{TP = A}，V:{TP = A，CN = B}}，.。。Nk = {P:{TP = A}，V:{TP = A，CN = B}}-所有节点均显示证书 A，所有节点都接受证书 A （TP）或 B （CN）

此时，我们可以继续使用后续升级来更改展示规则：
  - 完成升级后，域0： N0 = {P:{CN = B}，V:{TP = A，CN = B}}，.。。Nk = {P:{TP = A}，V:{TP = A，CN = B}}-UD0 中的节点将显示 CN 找到的证书 B，并接受带有指纹 A 或公用名 B 的证书;所有其他节点均存在，并只接受指纹选择的证书 A
  - 完成最后一个升级域时： N0 = {P:{CN = B}，V:{TP = A，CN = B}}，.。。Nk = {P:{CN = B}，V:{TP = A，CN = B}}-所有节点都显示了 CN 找到的证书 B，所有节点都接受证书 A （TP）或 B （CN）
    
完成阶段2还会将群集转换为基于名称的常用证书;可在后续群集升级中删除基于指纹的验证声明。

> [!NOTE]
> 在 Azure Service Fabric 群集中，上面提供的工作流由 Service Fabric 资源提供程序进行协调;群集所有者还负责根据所指示的规则（表示或验证）将证书预配到群集中，建议在多个步骤中执行更改。

在单独的文章中，我们将介绍在 Service Fabric 群集中管理和设置证书的主题。

## <a name="troubleshooting-and-frequently-asked-questions"></a>故障排除和常见问题
尽管在 Service Fabric 群集中调试与身份验证相关的问题并不容易，但我们 hopeful 的提示和提示可能会有所帮助。 开始调查的最简单方法是在群集的节点上检查 Service Fabric 事件日志，而不是只需显示出现症状的节点，而不一定是已启动但无法连接到其中某个邻居的节点。 在 Windows 上，重要性的事件通常分别记录在 "应用程序和服务 Logs\Microsoft-ServiceFabric\Admin" 或 "操作" 通道下。 有时，[启用 CAPI2 日志记录](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)可能会很有帮助，以便捕获有关证书验证、CRL/CTL 检索等内容的更多详细信息（请记得在完成重现后禁用该日志记录）。

导致身份验证问题的群集中出现故障的典型症状如下： 
  - 节点关闭/循环 
  - 连接尝试被拒绝
  - 连接尝试超时

每个症状可能由不同的问题引起，相同的根本原因可能会显示不同的表现形式;这样，我们将只列出典型问题的小示例，并提供修复这些问题的建议。 

* 节点可以交换消息，但无法连接。 中断连接尝试的一个可能原因是 "证书不匹配" 错误-Service Fabric 到 Service Fabric 连接中的一个参与方正在演示一个证书，该证书将无法通过收件人的验证规则。 可能伴随以下两个错误之一： 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  进一步诊断/调查：在每个尝试连接的节点上，确定要呈现的证书;检查证书，并尝试并模拟验证规则（检查指纹或公用名是否相等，如果指定，则检查颁发者指纹）。

  另一个常见的错误代码可能是：
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  在这种情况下，证书是由公用名声明的，其中的任何一种都适用：
    - 未固定颁发者，并且根证书不受信任，或者
    - 已固定颁发者，但声明不包括此证书的直接颁发者的指纹

* 节点已启动，但无法连接到其他节点;其他节点不接收来自失败节点的入站流量。 在这种情况下，可能无法在本地节点上加载证书。 查找以下错误：
  - 找不到证书-确保可通过 LocalMachine\My （或指定）证书存储的内容来解析显示规则中声明的证书。 
    可能的失败原因包括： 
      - 指纹声明中的字符无效
      - 未安装证书
      - 证书已过期
      - 公用名声明包括前缀 "CN ="
      - 声明指定了通配符，且证书存储区中不存在完全匹配项（声明： CN = *. mydomain，实际证书： CN = server）

  - 未知凭据-表示缺少与证书相对应的私钥，通常附带错误代码： 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    若要解决此情况，请检查是否存在私钥;验证是否已向 SFAdmins 授予对私钥的 "读取"、"执行" 访问权限。

  - 错误的提供程序类型-表示加密新一代（CNG）证书（"Microsoft 软件密钥存储提供程序"）;目前，Service Fabric 仅支持 CAPI1 证书。 通常伴随错误代码：
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    若要解决此情况，请使用 CAPI1 （例如 "Microsoft 增强 RSA 和 AES 加密提供程序"）提供程序重新创建群集证书。 有关加密提供程序的更多详细信息，请参阅[了解加密提供程序](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

