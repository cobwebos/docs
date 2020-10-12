---
title: Service Fabric 群集中基于 X.509 证书的身份验证
description: 了解 Service Fabric 群集中基于证书的身份验证，以及如何检测、缓解和修复与证书相关的问题。
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 36717f526f88af753f3929d62e84ee65be4320e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86259034"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>Service Fabric 群集中基于 X.509 证书的身份验证

本文是对 [Service Fabric 群集安全性](service-fabric-cluster-security.md)简介的补充，详细介绍了 Service Fabric 群集中基于证书的身份验证。 假设读者熟悉基本的安全性概念，以及 Service Fabric 公开的用于控制群集安全性的控制措施。  

本文的主题包括：

* 有关基于证书的身份验证的基础知识
* 标识以及各自的角色
* 证书配置规则
* 故障排除和常见问题解答

## <a name="certificate-based-authentication-basics"></a>有关基于证书的身份验证的基础知识
简单回顾以前的内容：在安全领域，证书是将有关某个实体（使用者）的信息绑定到其拥有的一对非对称加密密钥，从而构成公钥加密核心构造的一种机制。 证书代表的密钥可用于保护数据，或用于证明密钥持有者的身份；与公钥基础结构 (PKI) 系统结合使用时，证书可以代表其使用者的其他特征，例如 Internet 域的所有权，或者证书颁发者（称为证书颁发机构，简称 CA）向其使用者授予的特定特权。 应用证书的一个常见目的是支持传输层安全性 (TLS) 加密协议，以便通过计算机网络进行安全通信。 具体而言，客户端和服务器使用证书来确保其通信的隐私性和完整性，以及执行相互身份验证。

在 Service Fabric 中，群集的基础层（联合层）也是基于 TLS（以及其他协议）构建的，目的是实现可靠且安全的参与节点网络。 通过 Service Fabric 客户端 API 与群集建立的连接也使用 TLS 来保护流量，以及建立参与方的标识。 具体而言，在 Service Fabric 中用于身份验证时，证书可用于证明以下声明：a) 证书凭据的出示者拥有证书的私钥 b) 证书的 SHA-1 哈希（“指纹”）与群集定义中包含的声明匹配，或 c) 证书的可分辨“使用者公用名”与群集定义中包含的声明匹配，且证书的颁发者是已知的或受信任的。

在上面的列表中，“b”俗称为“指纹固定”；在这种情况下，声明是指特定的证书，身份验证方案的强度依赖于这么一个前提：在计算上无法伪造一个可以生成与另一证书相同的哈希值，但在所有其他方面仍是格式标准的有效对象的证书。 项“c”代表另一种证书声明形式，其方案强度依赖于证书使用者和颁发机构的组合。 在这种情况下，声明是指某类证书 - 具有相同特征的任意两个证书被视为完全等效。 

以下部分将深入说明 Service Fabric 运行时如何使用和验证证书来确保群集安全性。

## <a name="identities-and-their-respective-roles"></a>标识以及各自的角色
在深入了解身份验证或信道保护的详细信息之前，必须列出参与者及其在群集中扮演的相应角色：
- Service Fabric 运行时（称为“系统”）：提供代表群集的抽象和功能的服务集。 表示系统实例之间的群集内通信时，我们将使用术语“群集标识”；当群集表示作为来自群集外部的流量的接收方/目标时，我们将使用术语“服务器标识”。
- 托管应用程序（称为“应用程序”）：群集所有者提供的代码，在群集中协调和执行
- 客户端：允许连接到的实体，可根据群集配置在群集中执行功能。 我们区分两种级别的权限，分别为“用户”和“管理员”。 “用户”客户端主要限制为执行只读操作（但并非所有只读功能），而“管理员”客户端对群集功能的访问权限则不受限制。 （有关更多详细信息，请参阅 [Service Fabric 群集中的安全角色](service-fabric-cluster-security-roles.md)。）
- （仅限 Azure）Service Fabric 服务协调和公开 Service Fabric 群集的操作与管理控制权，简称为“服务”。 根据环境，“服务”可以指 Azure Service Fabric 资源提供程序，或 Service Fabric 团队拥有和操作的其他资源提供程序。

在安全群集中，可为其中的每个角色配置其自身的不同的标识，并将其声明为预定义角色名称及其相应凭据的配对。 Service Fabric 支持将凭据声明为证书或基于域的服务主体。 （也支持基于 Windows/Kerberos 的标识，但此内容超出了本文的范围；请参阅 [Service Fabric 群集中基于 Windows 的安全性](service-fabric-windows-cluster-windows-security.md)。）在 Azure 群集中，还可将客户端角色声明为[基于 Azure Active Directory 的标识](service-fabric-cluster-creation-setup-aad.md)。

如前所述，Service Fabric 运行时在群集中定义两种级别的特权：“管理员”和“用户”。 管理员客户端和“系统”组件均可使用“管理员”特权运行，因此无法区分彼此。 在群集中建立连接/与群集建立连接后，Service Fabric 运行时会为经过身份验证的调用方授予这两种角色之一，作为后续授权的基础。 后续部分将深入探讨身份验证。

## <a name="certificate-configuration-rules"></a>证书配置规则
### <a name="validation-rules"></a>验证规则
Service Fabric 群集的安全性设置大致描述以下方面：
- 身份验证类型；这是群集在创建时的不可变特征。 此类设置的示例包括“ClusterCredentialType”和“ServerCredentialType”，允许的值为“none”、“x509”或“windows”。 本文重点介绍 x509 类型身份验证。
- （身份验证）验证规则；这些设置由群集所有者设置，描述给定角色应接受的凭据。 接下来将深入探讨相关示例。
- 用于微调或轻微改变身份验证结果的设置；示例包括用于限制（取消限制）证书吊销列表的强制实施的标志，等等。

> [!NOTE]
> 下面提供的群集配置示例摘自 XML 格式的群集清单，该格式是最精炼的格式，直接支持本文所述的 Service Fabric 功能。 可以直接以群集定义的 JSON 表示形式来表示相同的设置，无论是使用独立的 JSON 群集清单，还是使用 Azure 资源管理模板。

证书验证规则由以下元素组成：
- 相应的角色：客户端、管理员客户端（特权角色）
- 角色接受的凭据，由指纹或使用者公用名声明

#### <a name="thumbprint-based-certificate-validation-declarations"></a>基于指纹的证书验证声明
对于基于指纹的验证规则，将按如下方式对请求在群集中建立连接/与群集建立连接的调用方所出示的凭据进行验证：
  - 凭据是格式标准的有效证书：可以生成其链，且签名匹配
  - 证书有时效性 (NotBefore <= now < NotAfter)
  - 证书的 SHA-1 哈希与声明匹配，匹配方式是进行不区分大小写的字符串比较（排除所有空格）

对于基于指纹的声明，将会取消显示在链生成或验证过程中遇到的任何信任错误，过期的证书除外 - 不过，对于证书过期的情况，还需要遵守一些规定。 具体而言，对于与吊销状态为未知或脱机、不受信任的根、密钥用法无效相关的失败，部分链将被视为非灾难性错误；这种情况的前提是证书只是密钥对的信封 - 安全性依赖于群集所有者已经采取了保护私钥的措施这一事实。

摘自群集清单的以下内容演示了一组基于指纹的验证规则：

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

根据前面所述，上面的每个条目都表示一个特定的标识；每个条目还允许以逗号分隔的字符串列表形式指定多个值。 在此示例中，在成功验证传入凭据后，将为具有 SHA-1 指纹“d5ec...4264”的证书的出示者授予“管理员”角色；相反，将为使用证书“7c8f...01b0”进行身份验证的调用方授予“用户”角色，该角色主要限制为执行只读操作。 接受出示指纹为“abcd...1234”或“ef01...5678”的证书的入站调用方作为群集中的对等节点。 最后，连接到群集管理终结点的客户端会要求服务器证书的指纹为“ef01...5678”。 

如前所述，Service Fabric 在接受过期证书方面做出了规定；原因是证书的生存期有限，在由指纹（表示特定的证书实例）声明时，允许证书过期会导致连接到群集失败，或导致群集彻底崩溃。 很容易忘记或忽略轮换指纹固定的证书，遗憾的是，在这种情况下进行恢复却很困难。

为此，群集所有者可以显式指明应将指纹声明的自签名证书视为有效，如下所示：

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
此行为不会扩展到 CA 颁发的证书；如果出现那种情况，当已吊销的、已知泄露的过期证书不再包含在 CA 证书吊销列表中时，该证书会立即变成“有效”证书，从而带来安全风险。 使用自签名证书时，群集所有者被视为负责保护证书私钥的唯一一方，而使用 CA 颁发的证书时则不存在这种情况 - 群集所有者可能不知道其证书是如何或何时声明为已泄露的。

#### <a name="common-name-based-certificate-validation-declarations"></a>基于公用名的证书验证声明
基于公用名的声明采用以下形式之一：
- （仅限）使用者公用名
- 进行了颁发者固定的使用者公用名

让我们首先考虑通过群集清单的摘录内容演示这两种声明样式：
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
声明分别表示服务器和群集标识；请注意，基于 CN 的声明在群集清单中具有自身的节，而不同于标准“安全性”。 在这两个声明中，“Name”表示证书的可分辨使用者公用名，“Value”字段表示预期的颁发者，如下所述：

- 对于第一种情况，声明指明服务器证书可分辨使用者的公用名元素预期与字符串“server.demo.system.servicefabric.azure-int”匹配；空的“Value”字段表示证书链的根预期在验证服务器证书的节点/计算机上受信任；在 Windows 上，这意味着该证书可以链接到“受信任根 CA”存储中安装的任何证书；
- 对于第二种情况，声明指明，如果证书的公用名与字符串“cluster.demo.system.servicefabric.azure-int”匹配，并且证书直接颁发者的指纹与“值”字段中的某个逗号分隔条目匹配，则接受该证书的出示者作为群集中的对等节点。  （此规则类型俗称为“进行了颁发者固定的公用名”。）

无论哪种情况，都会生成证书链，且该链预期不存在错误；即，吊销错误、部分链或时间无效信任错误都被视为灾难性错误，在此情况下证书验证会失败。 固定颁发者会导致将“不受信任的根”状态视为非灾难性错误；不过，这在表面上只是一种更严格的验证形式，因为它允许群集所有者将授权的/接受的颁发者集约束为自身的 PKI。

生成证书链后，将使用声明的使用者作为远程名称根据标准 TLS/SSL 策略来验证该链。如果某个证书的使用者公用名或其任何使用者可选名称与群集清单中的 CN 声明相匹配，则会将该证书视为匹配。 这种情况下支持通配符，字符串匹配不区分大小写。

（我们应该澄清，对于证书声明的每种密钥用法类型，可以执行上述序列；如果证书指定了客户端身份验证密钥用法，则首先为某个客户端角色生成并评估该链。 如果成功，即表示评估完成且验证成功。 如果证书未声明客户端身份验证用法或者验证失败，则 Service Fabric 运行时会生成并评估服务器身份验证链。）

若要完成本示例，请参阅以下摘录，了解如何按公用名声明客户端证书：
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

以上声明分别对应于管理员标识和用户标识；以这种方式声明的证书的验证与前面示例中群集和服务器证书的验证完全相同。

> [!NOTE]
> 基于公用名的声明旨在简化群集证书的轮换，在一般情况下还可以简化其管理。 但是，我们建议你遵循以下建议，以确保群集的持续可用性和安全性：
  * 优先采用颁发者固定，而不是依赖于受信任的根
  * 避免混合来自不同 PKI 的颁发者
  * 确保所有预期的颁发者已列在证书声明中；颁发者不匹配会导致验证失败
  * 确保 PKI 的证书策略终结点可发现、可用且可访问 - 这意味着，AIA、CRL 或 OCSP 终结点已在叶证书中声明且可访问，因此可以完成证书链生成。

如果满足所有这些要求，收到在使用 X.509 证书保护的群集中建立连接的请求后，Service Fabric 运行时会使用群集的安全设置来验证远程方的凭据，如前所述；如果成功，则将调用方/远程方视为已通过身份验证。 如果凭据与多个验证规则匹配，则运行时将为调用方授予任何已匹配规则的最高特权角色。 

### <a name="presentation-rules"></a>出示规则
上一部分介绍了身份验证在受证书保护的群集中的工作原理；本部分将说明 Service Fabric 运行时本身如何发现和加载用于群集中通信的证书；我们称之为“出示”规则。

与验证规则一样，出示规则会指定一个角色和关联的凭据声明，后者通过指纹或公用名进行表述。 与验证规则不同，基于公用名的声明在颁发者固定方面没有规定；这可以提高灵活性和性能。 对于每个不同的节点类型，出示规则在群集清单的“NodeType”节中声明；设置是从群集的 Security 节中拆分的，使每个节点类型在单个节中具有自身的完整配置。 在 Azure Service Fabric 群集中，节点类型证书声明默认为其在群集定义的 Security 节中的相应设置。

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>基于指纹的证书出示声明
如前所述，Service Fabric 运行时会将自身的作为群集中其他节点的对等方的角色与作为群集管理操作的服务器的角色区分开来。 原则上可以区别性地配置这些设置，但在实践中，这些设置往往是一致的。 为方便说明，本文的余下内容假设设置是匹配的。

以摘自群集清单的以下内容为例：
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
“ClusterCertificate”元素演示完整架构，包括可选参数（“X509FindValueSecondary”）或具有相应默认值的参数（“X509StoreName”）；其他声明显示了缩写形式。 以上群集证书声明指明，类型为“nt1vm”的节点的安全设置是使用主要证书“cc71..1984”和辅助证书“49e2..19d6”初始化的；这两个证书预期可在 LocalMachine\'My' 证书存储（或 Linux 等效路径 *var/lib/sfcerts*）中找到。

#### <a name="common-name-based-certificate-presentation-declarations"></a>基于公用名的证书出示声明
还可以按使用者公用名声明节点类型证书，如以下示例所示：

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

对于任一类型的声明，Service Fabric 节点会在启动时读取配置，查找并加载指定的证书，并按证书的 NotAfter 属性以降序方式将证书排序；忽略已过期的证书，列表的第一个元素将选作此节点尝试建立的任何 Service Fabric 连接的客户端凭据。 （实际上，Service Fabric 会优先使用最后面的即将过期的证书。）

请注意，对于基于公用名的出示声明，如果在进行区分大小写的精确字符串比较的情况下，某个证书的使用者公用名等于声明的 X509FindValue（或 X509FindValueSecondary）字段，则将该证书视为匹配。 这与验证规则相反。验证规则支持通配符匹配，以及不区分大小写的字符串比较。  

### <a name="miscellaneous-certificate-configuration-settings"></a>其他证书配置设置
前面提到，Service Fabric 群集的安全设置还允许对身份验证代码的行为进行细微更改。 尽管有关 [Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)的文章提供了全面且最新的设置列表，但本文将对少量的所选安全设置的涵义进行扩展，以完全公开基于证书的身份验证。 对于每项设置，我们会介绍其意图、默认值/行为、对身份验证的影响以及可接受的值。

如前所述，证书验证始终意味着要生成并评估证书链。 对于 CA 颁发的证书，这种表面上简单的 OS API 调用通常需要对颁发者 PKI 的各个终结点进行多次出站调用、对响应进行缓存，以及执行其他操作。 由于证书验证调用在 Service Fabric 群集中非常普遍，PKI 终结点中出现任何问题都可能导致群集可用性下降或完全中断。 尽管无法抑制出站调用（如需此方面的详细信息，请参阅下面的“常见问题解答”部分），但可以使用以下设置来隐藏由于 CRL 调用失败而导致的验证错误。

  * CrlCheckingFlag -“Security”节中转换为 UINT 的字符串。 Service Fabric 使用此设置的值通过更改链生成行为来隐藏证书链状态错误；此值将作为“dwFlags”参数传递给 Win32 CryptoAPI [CertGetCertificateChain](/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) 调用，并可设置为该函数接受的任何有效标志组合。 值为 0 会强制 Service Fabric 运行时忽略任何信任状态错误 - 不建议使用此值，因为会导致严重的安全风险。 默认值为 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT)。

  何时使用：使用格式不全面的自签名证书或开发人员证书进行本地测试/没有适当的公钥基础结构用于支持证书。 还可以在 PKI 之间的过渡期间，在与外界隔绝的环境中用作缓解措施。

  如何使用：我们将使用一个强制吊销检查仅访问缓存 URL 的示例。 假设：
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  那么，群集清单中的声明将变为：
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError -“Security”节中默认值为“false”的布尔值。 表示一个快捷方式，用于抑制“脱机吊销”链生成错误状态（或后续链策略验证错误状态）。

  何时使用：本地测试，或者使用不是由适当 PKI 支持的开发人员证书。 在与外界隔绝的环境中用作缓解措施，或者在 PKI 已知不可访问时使用。

  如何使用：
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  其他值得注意的设置（都在“Security”节中）：
  * AcceptExpiredPinnedClusterCertificate - 在专用于基于指纹的证书验证的部分中已讨论；允许接受已过期的自签名群集证书。 
  * CertificateExpirySafetyMargin - 时间间隔，以证书的 NotAfter 时间戳之前的分钟数为单位，在此期间，证书被视为存在过期风险。 Service Fabric 会监视群集证书，并对其剩余可用性定期发出运行状况报告。 在“安全”时间间隔内，这些运行状况报告将提升为“警告”状态。 默认值为 30 天。
  * CertificateHealthReportingInterval - 控制与群集证书剩余有效时间相关的运行状况报告的频率。 发出报告时，每个这样的时间间隔只发出一次。 值以秒表示，默认值为 8 小时。
  * EnforcePrevalidationOnSecurityChanges - 布尔值，控制检测到安全设置更改后的群集升级行为。 如果设置为“true”，则群集升级会尝试确保与任何表示规则匹配的证书中至少有一个可以通过相应的验证规则。 在将新设置应用于任何节点之前会执行预验证，但它仅在启动升级时承载群集管理器服务主要副本的节点上运行。 在撰写本文时，该设置的默认值为“false”；在从 7.1 开始的运行时版本中，对于新的 Azure Service Fabric 群集，该设置将为“true”。
 
### <a name="end-to-end-scenario-examples"></a>端到端方案（示例）
我们已了解出示规则、验证规则和微调标志，但所有这些设置如何协同工作呢？ 本部分将通过两个端到端示例，演示如何利用安全设置来进行安全的群集升级。 请注意，本文并非综合性论文，不讨论如何在 Service Fabric 中正确地进行证书管理。如需这方面的内容，请查看该主题的相关文章。

出示规则和验证规则的隔离引发了这样一个明显的问题（或忧虑）：两者是否可以相互背离，如果可以，会有什么后果？ 一个节点选择的身份验证证书确实有可能通不过另一个节点的验证规则。 事实上，这种偏差是身份验证相关事件的主要原因。 同时，这些规则的隔离使得群集在升级期间能够继续运行，从而更改了群集的安全设置。 假设先作为一个首要步骤补充验证规则，所有群集节点会按新的设置融合，同时仍使用当前凭据。 

回顾前文，在 Service Fabric 群集中，升级过程会进行到（最多 5 个）“升级域”(UD)。 在给定的时间，只会升级/更改当前 UD 中的节点。仅当群集的可用性允许时，升级才会继续进行到下一 UD。 （有关详细信息，请参阅 [Service Fabric 群集升级](service-fabric-cluster-upgrade.md)和同一主题的其他文章。）证书/安全性更改的风险特别高，因为这种更改可能会从群集中隔离节点，或让群集处于仲裁丢失的边缘状态。

我们将使用以下表示法来描述节点的安全设置：

Nk: {P:{TP=A}, V:{TP=A}}，其中：
  - “Nk”表示升级域 *k* 中的节点
  - “P”表示节点的当前出示规则（假设仅引用群集证书）； 
  - “V”表示节点的当前验证规则（仅限群集证书）
  - “TP=A”表示基于指纹的声明 (TP)，其中的“A”是证书指纹
  - “CN=B”表示基于公用名的声明 (CN)，其中的“B”是证书的使用者公用名 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>轮换按指纹声明的群集证书
以下序列描述如何使用 2 阶段升级过程来安全地引入按指纹声明的辅助群集证书；第一个阶段在验证规则中引入新的证书声明，第二个阶段在出示规则中引入该声明：
  - 初始状态：N0 = {P:{TP=A}, V:{TP=A}}, ...Nk = {P:{TP=A}, V:{TP=A}} - 群集处于静止状态，所有节点共享一个通用配置
  - 完成升级域 0 后：N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ...Nk = {P:{TP=A}, V:{TP=A}} - UD0 中的节点会出示证书 A，并接受证书 A 或 B；所有其他节点仅出示并接受证书 A
  - 完成最后一个升级域后：N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ...Nk = {P:{TP=A}, V:{TP=A, TP=B}} - 所有节点出示证书 A，所有节点接受证书 A 或 B
      
此时，群集再次进入平衡状态，第二个升级阶段/更改安全设置的操作可以开始：
  - 完成升级域 0 后：N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ...Nk = {P:{TP=A}, V:{TP=A, TP=B}} - UD0 中的节点开始出示证书 B，该证书由群集中的任何其他节点接受。
  - 完成最后一个升级域后：N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ...Nk = {P:{TP=A, TP=B}, V:{TP=A, TP=B}} - 所有节点已切换为出示证书 B。现在，可以通过后续的升级集在群集定义中停用/删除证书 A。

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>将群集从基于指纹的证书声明转换为基于公用名的证书声明
同样，更改证书声明类型（从指纹更改为公用名）遵循上述相同模式。 请注意，验证规则允许在同一群集定义中按指纹和公用名声明给定角色的证书。 不过，相比之下，出示规则只允许一种形式的声明。 顺便提一句，若要将群集证书从指纹转换为公用名，安全的做法是先按指纹引入目标证书，然后将该声明更改为基于公用名的声明。 在以下示例中，我们假设指纹“A”和使用者公用名“B”引用同一证书。 

  - 初始状态：N0 = {P:{TP=A}, V:{TP=A}}, ...Nk = {P:{TP=A}, V:{TP=A}} - 群集处于静止状态，所有节点共享一个通用配置，A 是主证书指纹
  - 完成升级域 0 后：N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ...Nk = {P:{TP=A}, V:{TP=A}} - UD0 中的节点会出示证书 A，并接受包含指纹 A 或公用名 B 的证书；所有其他节点仅出示并接受证书 A
  - 完成最后一个升级域后：N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ...Nk = {P:{TP=A}, V:{TP=A, CN=B}} - 所有节点出示证书 A，所有节点接受证书 A (TP) 或 B (CN)

此时，我们可以继续通过后续升级来更改出示规则：
  - 完成升级域 0 后：N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ...Nk = {P:{TP=A}, V:{TP=A, CN=B}} - UD0 中的节点会出示按 CN 找到的证书 B，并接受包含指纹 A 或公用名 B 的证书；所有其他节点仅出示并接受按指纹选择的证书 A
  - 完成最后一个升级域后：N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ...Nk = {P:{CN=B}, V:{TP=A, CN=B}} - 所有节点会出示按 CN 找到的证书 B，所有节点会接受证书 A (TP) 或 B (CN)
    
完成阶段 2 也意味着将群集转换为基于公用名的证书；在后续群集升级中可以删除基于指纹的验证声明。

> [!NOTE]
> 在 Azure Service Fabric 群集中，上述工作流由 Service Fabric 资源提供程序进行协调；群集所有者仍需负责按指定规则（出示或验证规则）将证书预配到群集中。我们建议通过多个步骤执行更改。

我们会在单独的文章中介绍关于如何在 Service Fabric 群集中管理和预配证书的主题。

## <a name="troubleshooting-and-frequently-asked-questions"></a>故障排除和常见问题解答
尽管在 Service Fabric 群集中调试与身份验证相关的问题并不容易，但以下提示和技巧也许会有帮助。 若要开始进行调查，最简单的方法是检查群集节点（不一定只是表现了症状的节点，也包括已启动但无法连接到某个邻居的节点）上的 Service Fabric 事件日志。 在 Windows 上，重要事件通常分别记录在“Applications and Services Logs\Microsoft-ServiceFabric\Admin”或“Operational”路径下。 有时，[启用 CAPI2 日志记录](/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)可能会有帮助，这样可以捕获有关证书验证、CRL/CTL 检索等方面的更多详细信息。（在完成问题重现后，请记得禁用此功能，因为记录的日志非常详细。）

遇到身份验证问题的群集中表现的典型症状如下： 
  - 节点关闭/循环启动 
  - 拒绝连接尝试
  - 连接尝试超时

每种症状可能由不同的问题造成，相同的根本原因可能表现出不同的症状；因此，我们只是列出了典型问题的简要示例，以及解决这些问题的建议方法。 

* 节点可以交换消息，但无法进行连接。 终止连接尝试的可能原因是发生了“证书不匹配”错误 - Service Fabric 到 Service Fabric 连接中的一方所出示的证书未通过接收方的验证规则。 可能会伴随以下两个错误之一： 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  若要进一步进行诊断/调查，请执行以下操作：在尝试连接的每个节点上，确定出示了哪个证书；检查该证书，并尝试模拟验证规则（检查指纹或公用名是否相等，并检查颁发者指纹（如果已指定））。

  伴随的另一个常见错误代码可能是：
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  在这种情况下，证书是按公用名声明的，并存在以下任一情况：
    - 颁发者未固定，根证书不受信任，或者
    - 颁发者已固定，但声明不包含此证书的直接颁发者的指纹

* 某个节点已启动，但无法连接到其他节点；其他节点不接收来自有故障节点的入站流量。 在这种情况下，本地节点上的证书加载可能会失败。 查看以下错误：
  - 找不到证书 - 确保可以按照 LocalMachine\My 证书存储（或指定的证书存储）的内容解析出示规则中声明的证书。 
    可能的失败原因包括： 
      - 指纹声明中的字符无效
      - 未安装证书
      - 证书已过期
      - 公用名声明包含前缀“CN=”
      - 声明指定了通配符，但证书存储中没有完全匹配项（声明：CN=*.mydomain.com，实际证书：CN=server.mydomain.com）

  - 未知凭据 - 表示缺少与证书对应的私钥，通常会伴随以下错误代码： 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    若要修正此问题，请检查是否存在私钥；验证是否已向 SFAdmins 授予了对私钥的“read|execute”访问权限。

  - 错误的提供程序类型 - 表示新一代加密 (CNG) 证书（“Microsoft 软件密钥存储提供程序”）；目前 Service Fabric 仅支持 CAPI1 证书。 通常伴随以下错误代码：
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    若要修正此问题，请使用 CAPI1（例如“Microsoft 增强型 RSA 和 AES 加密提供程序”）提供程序重新创建群集证书。 有关加密提供程序的更多详细信息，请参阅 [Understanding Cryptographic Providers](/windows/win32/seccertenroll/understanding-cryptographic-providers)（了解加密提供程序）
