---
title: Service Fabric 群集中的证书管理
description: 了解如何在使用 x.509 证书保护的 Service Fabric 群集中管理证书。
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: ecdeb5c9e30c176e2f3525f8efeb861d9210b202
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196239"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Service Fabric 群集中的证书管理

本文介绍了用于保护 Azure Service Fabric 群集中通信安全的证书的管理方面，并补充了[Service Fabric 群集安全](service-fabric-cluster-security.md)的简介，以及[Service Fabric 中基于 x.509 证书的身份验证](cluster-security-certificates.md)的说明。 假设读者熟悉基本的安全概念，以及 Service Fabric 公开的控件来配置群集的安全性。  

此标题下面涵盖的方面：

* 什么是 "证书管理"？
* 证书管理中涉及的角色和实体
* 证书旅程
* 深入探讨示例
* 故障排除和常见问题

但首先，免责声明：本文试图将理论端与动手示例配对，这需要、具体的服务、技术等。 由于就前往的一部分是 Microsoft 内部的，因此我们将介绍特定于 Microsoft Azure 的服务、技术和产品。 欢迎在评论部分中询问说明或指导，其中 Microsoft 特定的详细信息不适用于您的案例。

## <a name="defining-certificate-management"></a>定义证书管理
正如我们在[随附文章](cluster-security-certificates.md)中看到的，证书是一个加密对象，它实质上是将非对称密钥对与描述它所表示的实体的属性绑定在一起。 不过，它也是一个 "perishable" 对象，因为它的生存期是有限的，并且容易受到折衷-意外泄露或成功利用会使证书从安全角度看起来毫无用处。 这意味着需要更改证书-定期或响应安全事件。 管理的另一个方面（和本身就是整个主题）是对证书私钥的保护，或者保护采购和预配证书中涉及的实体标识的机密。 我们引用用于获取证书的过程和过程，并安全（和安全）将它们传输到需要它们作为 "证书管理" 的位置。 某些管理操作（例如注册、策略设置和授权控制）超出了本文的范围。 还有其他一些功能，如预配、续订、重新加密或吊销，只是与 Service Fabric 相关。尽管如此，我们将在此解决这些问题，因为了解这些操作有助于正确保护一个群集的安全。 

其目标是尽可能自动实现证书管理，以确保群集的不间断可用性，并确保安全保证，因为该过程是用户接触的。 当前在 Azure Service Fabric 群集中可达到此目标;本文的其余部分将首先析构证书管理，稍后将重点介绍如何启用 autorollover。

具体而言，范围中的主题包括：
  - 在管理证书的上下文中，与所有者和平台之间的归属分隔相关的假设
  - 颁发给消费的证书的长管道
  - 证书轮换-原因、操作方法和时间
  - 可能会出现什么错误？

例如，保护/管理域名、注册到证书或设置授权控制来强制颁发证书的方面超出了本文的范围。 请参阅你最喜欢的公钥基础结构（PKI）服务的注册机构（RA）。 Microsoft 内部使用方：请联系 Azure 安全性。

## <a name="roles-and-entities-involved-in-certificate-management"></a>证书管理中涉及的角色和实体
Service Fabric 群集的安全方法是 "群集所有者声明它，Service Fabric 运行时强制执行"。 这意味着，几乎没有任何证书、密钥或参与群集功能的其他标识凭据来自服务本身;它们都是由群集所有者声明的。 此外，群集所有者还负责将证书预配到群集中、根据需要续订这些证书，并确保证书的安全性。 更具体地说，群集所有者必须确保：
  - 根据[显示规则](cluster-security-certificates.md#presentation-rules)，可以在该类型的每个节点上找到在群集清单的节点类型部分中声明的证书
  - 上面声明的证书安装了包含其对应私钥的证书
  - 在演示规则中声明的证书应传递[验证规则](cluster-security-certificates.md#validation-rules) 

Service Fabric，它将承担以下职责：
  - 查找/查找与群集定义中的声明匹配的证书  
  - 在 "需要" 的基础上向 Service Fabric 控制的实体授予相应私钥的访问权限
  - 严格根据已建立的安全性最佳实践和群集定义验证证书
  - 在即将过期的证书过期或执行证书验证的基本步骤时引发警报
  - 验证（到一定程度）主机的基础配置是否满足群集定义的与证书相关的方面 

这是因为证书管理负担（作为活动操作）仅限群集所有者。 在以下部分中，我们将详细介绍每个管理操作，以及可用机制及其对群集的影响。

## <a name="the-journey-of-a-certificate"></a>证书旅程
让我们快速地从颁发到 Service Fabric 群集上下文中使用证书的进度：

  1. 域所有者向 PKI 的 RA 注册要与后续证书关联的域或主题;反过来，证书构成了所说域或使用者的所有权证明
  2. 域所有者还在 RA 中指定了有权请求证书注册到指定域或使用者的授权请求方的标识;在 Microsoft Azure 中，默认标识提供者是 Azure Active Directory，授权的请求者由其对应的 AAD 标识（或通过安全组）指定。
  3. 然后，已获授权的请求者通过密钥管理服务注册到证书;在 Microsoft Azure 中，所选的 SMS 是 Azure Key Vault （AKV），它安全地存储和允许经授权的实体检索机密/证书。 AKV 还会根据关联的证书策略中的配置来续订/重新密钥证书。 （AKV 使用 AAD 作为标识提供者。）
  4. 授权检索器-我们将称为 "预配代理"-从保管库检索证书（包含其私钥），并将其安装在托管群集的计算机上
  5. Service Fabric 服务（在每个节点上运行提升）授予对证书的访问权限，以允许 Service Fabric 实体;这些组由本地组指定，并在 Servicefabricadministrators 也和 ServiceFabricAllowedUsers 之间剥离
  6. Service Fabric 运行时访问并使用证书建立联合身份验证，或通过授权客户端的入站请求进行身份验证
  7. 预配代理监视保管库证书，并在检测续订时触发预配流;然后，群集所有者会根据需要更新群集定义，以指示要在证书上滚动的意图。
  8. 预配代理或群集所有者还负责清除或删除未使用的证书
  
对于我们而言，以上序列中的前两个步骤在很大程度上是不相关的;唯一的连接是，证书的使用者公用名是在群集定义中声明的 DNS 名称。

这些步骤如下所示：请注意分别由指纹和公用名声明的证书之间的设置差异。

*图1。* 指纹声明的证书的颁发和预配流。
![预配指纹声明的证书][Image1]

*图2。* 使用者公用名声明的证书的颁发和预配流。
![设置使用者公用名声明的证书][Image2]

### <a name="certificate-enrollment"></a>证书注册
本主题将在 Key Vault[文档](../key-vault/create-certificate.md)中详细介绍;我们在此提供了一个有关连续性和更易于参考的概要。 继续使用 Azure 作为上下文，并使用 Azure Key Vault 作为机密管理服务，授权证书请求者必须至少具有保管库上的证书管理权限，并由保管库所有者授予;然后，请求者将注册到证书，如下所示：
    - 在 Azure Key Vault （AKV）中创建证书策略，该策略指定证书的域/主题、所需的颁发者、密钥类型和长度、预期密钥用法和更多;有关详细信息，请参阅[Azure Key Vault 中的证书](../key-vault/certificate-scenarios.md)。 
    - 使用上面指定的策略在同一保管库中创建证书;这反过来会将密钥对生成为保管库对象、使用私钥签名的证书签名请求，然后将该请求转发给指定的颁发者进行签名。
    - 颁发者（证书颁发机构）使用签名证书进行答复后，结果将合并到保管库中，并且证书可用于以下操作：
      - {vaultUri}/certificates/{name}：包含公钥和元数据的证书
      - {vaultUri}/keys/{name}：证书的私钥，可用于加密操作（wrap/解包、签名/验证）
      - 在 {vaultUri}/secrets/{name} 上：包含其私钥的证书，可作为不受保护的 pfx 或 pem 文件下载，这意味着保管库证书实际上是证书实例的时间行，共享策略。 将根据策略的生存期和续订属性创建证书版本。 强烈建议保管库证书不共享主体或域/dns 名称;群集从不同的保管库证书中设置证书实例可能会造成中断，但具有相同的主体，但具有完全不同的其他属性，如颁发者、密钥用法等。

此时，保管库中存在一个证书，可供使用。 之前：

### <a name="certificate-provisioning"></a>证书预配
我们提到了 "预配代理"，它是从保管库中检索证书（包含其私钥）并将其安装到群集的每个主机上的实体。 （请记住，Service Fabric 不会设置证书。）在我们的上下文中，群集将托管在 Azure Vm 和/或虚拟机规模集的集合上。 在 Azure 中，可以使用以下机制来实现从保管库到 VM/VMSS 的证书的预配，假设预配代理以前已被保管库所有者授予保管库的 "get" 权限： 
  - 即席：操作员从保管库中检索证书（作为 pfx/PKCS #12 或 pem），并将其安装在每个节点上
  - 在部署过程中作为虚拟机规模集 "机密"：计算服务使用代表操作员的第一方标识、启用模板部署的保管库中的证书进行检索，并将其安装在虚拟机规模集的每个节点上（[如下所示](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)）;请注意，这只允许设置版本控制的机密
  - 使用[KEY VAULT VM 扩展](../virtual-machines/extensions/key-vault-windows.md);这样，便可以使用版本较少的声明来预配证书，并定期刷新观察到的证书。 在这种情况下，VM/VMSS 应该具有一个[托管标识](../virtual-machines/windows/security-policy.md#managed-identities-for-azure-resources)，该标识已被授予对包含所观察证书的保管库的访问权限。

出于多种原因，不建议使用即席机制，范围从安全性到可用性，稍后不会进行讨论;有关详细信息，请参阅[虚拟机规模集中的证书](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)。

VMSS-/Compute-based 预配提供安全性和可用性优势，但它也提供了限制。 它需要-通过设计-将证书声明为版本控制的机密，使其仅适用于使用指纹声明的证书保护的群集。 与此相反，基于 Key Vault VM 扩展的设置将始终安装每个观察到的证书的最新版本，这使得它仅适用于使用使用者公用名声明的证书保护的群集。 若要强调，请不要对实例声明的证书使用 autorefresh 预配机制（例如 KVVM 扩展），否则失去可用性的风险会很大。

可能存在其他配置机制;Azure Service Fabric 群集目前已接受上述的。

### <a name="certificate-consumption-and-monitoring"></a>证书使用情况和监视
如前文所述，Service Fabric 运行时负责查找和使用在群集定义中声明的证书。 [基于证书的身份验证一](cluster-security-certificates.md)文详细介绍了 Service Fabric 如何分别实现演示和验证规则，并且在此不会进行此介绍。 我们将查看访问权限和权限授予以及监视。

请记住，Service Fabric 中的证书用于多种目的，从联合层中的相互身份验证到管理终结点的 TLS 身份验证;这需要不同的组件或系统服务才能访问证书的私钥。 Service Fabric 运行时定期扫描证书存储，并查找每个已知的表示规则的匹配项;对于每个匹配的证书，将找到相应的私钥，并更新其自由访问控制列表，使其包含权限（通常是读取和执行）以包括所需的相应标识。 （此过程非正式地称为 "Acl"。）此过程在1分钟的时间段内运行，并且还介绍了 "应用程序" 证书，如用于对设置或终结点证书进行加密的证书。 Acl 遵循演示规则，因此请务必记住，指纹声明的证书和未进行后续群集配置更新的 autorefreshed 将不可访问。    

### <a name="certificate-rotation"></a>证书轮换
请注意： IETF [RFC 3647](https://tools.ietf.org/html/rfc3647)正式将[续订](https://tools.ietf.org/html/rfc3647#section-4.4.6)定义为与要替换的证书具有相同属性的证书的颁发-颁发者、使用者的公钥和信息，并使用新密钥对[重新加密](https://tools.ietf.org/html/rfc3647#section-4.4.7)作为证书的颁发，而不限制颁发者是否可能会更改。 考虑到这一点可能很重要（假设证书的使用者公用名与颁发者固定的情况），我们将选择非特定术语 "轮换" 来涵盖这两种方案。 （请记住，在非正式情况下，"续订" 指的是重新加密。） 

之前我们曾见到 Azure Key Vault 支持自动证书轮替：关联证书策略定义时间点，无论是在保管库中轮换证书时，是在过期前的天数还是总生存期的百分比。 必须在此时间点之后调用预配代理，在以前的证书过期之前，将此新证书分发到群集的所有节点。 当证书的到期日期（以及当前在群集中使用的）到期日期早于预先确定的时间间隔时，Service Fabric 将提供相应的运行状况警告。 配置为观察保管库证书的自动预配代理（即 KeyVault VM 扩展）将定期轮询保管库，检测旋转，并检索和安装新证书。 通过 VM/VMSS ' 机密 ' 功能进行的设置将需要一个授权操作员，以使用与新证书相对应的版本控制 KeyVault URI 更新 VM/VMSS。

在这两种情况下，旋转的证书现已预配到所有节点，并且我们介绍了用于检测循环 Service Fabric 采用的机制。接下来，让我们看看会发生什么情况-假设应用于使用者公用名声明的群集证书的旋转（所有适用时间都适用于此编写，并 Service Fabric 运行时版本7.1.409）：
  - 对于内部和群集中的新连接，Service Fabric 运行时将查找并选择具有最远到期日期（证书的 "NotAfter" 属性，通常缩写为 "na"）的匹配证书。
  - 现有连接将保持活动/允许自然过期或终止;将通知内部处理程序存在新的匹配项

这将转换为以下重要的观察：
  - 如果续订证书的到期日期早于当前使用的证书，则可能会忽略该证书。
  - 群集的可用性，或托管的应用程序的可用性优先于指令来旋转证书;群集将最终汇聚到新证书，但不会有时间保证。 如下所示：
  - 它可能不会立即被认为旋转后的证书完全取代了其前置。确保（对于群集证书）重新启动主机的唯一方法。 请注意，重新启动 Service Fabric 节点并不是足够的，因为在群集中形成租约连接的内核模式组件不会受到影响。 另请注意，重新启动 VM/VMSS 可能会导致暂时丢失可用性。 （对于应用程序证书，只需重新启动各自的应用程序实例即可。）
  - 引入不符合验证规则的重新键控证书可以有效地打破群集。 这种情况最常见的示例是：意外的颁发者：群集证书由使用者的公用名声明，并具有颁发者固定，但旋转的证书是由新的/未声明的颁发者颁发的。     

### <a name="certificate-cleanup"></a>证书清除
目前，Azure 中没有用于显式删除证书的预配。 通常不需要确定是否在给定的时间使用了给定的证书。 这对于应用程序证书比群集证书更难。 Service Fabric 本身，而不是预配代理，则在任何情况下都不会删除用户声明的证书。 对于标准预配机制：
  - 声明为 VM/VMSS 机密的证书将被预配，前提是这些证书在 VM/VMSS 定义中被引用，它们可从保管库中检索（删除保管库机密/证书将导致后续 VM/VMSS 部署失败; 同样，禁用保管库中的密钥版本也将导致引用该密钥版本的 VM/VMSS 部署失败）
  - 先前版本的通过 KeyVault VM 扩展预配的证书可能存在于 VM/VMSS 节点上，也可能不存在。 代理仅检索和安装当前版本，不删除任何证书。 重新映像节点（通常每月发生一次）会将证书存储重置为操作系统映像的内容，因此，将隐式删除以前的版本。 但请考虑，纵向扩展虚拟机规模集将仅导致正在安装的观察证书的当前版本;不要假设同质化已安装的证书。   

## <a name="simplifying-management---an-autorollover-example"></a>简化管理-autorollover 示例
我们介绍了各种机制、限制、更复杂的规则和定义，并使可怕预测中断。 或许还需要说明如何设置自动证书管理，以避免所有这些问题。 我们将在 PaaSv2 虚拟机规模集上运行的 Azure Service Fabric 群集的上下文中执行此操作，使用机密管理 Azure Key Vault 并利用托管标识，如下所示：
  - 证书验证从指纹固定更改为使用者 + 颁发者固定：给定颁发者中具有给定主体的任何证书都是相同的受信任证书
    - 证书注册到受信任的存储区中并从受信任的存储区中获取（Key Vault），并由代理进行刷新-在本例中为 KeyVault VM 扩展
    - 证书的预配将从部署时间和基于版本的（如 ComputeRP）更改为后期部署，并使用不限版本的 KeyVault Uri
    - 通过用户分配的托管标识授予对 KeyVault 的访问权限;在部署过程中，将创建 UA 标识并将其分配给虚拟机规模集
    - 部署完成后，代理（KV VM 扩展）将轮询并刷新虚拟机规模集的每个节点上的观察到的证书;因此，证书轮换完全自动，因为 SF 会自动选取最远的有效证书

序列可完全脚本化/自动执行，并允许用户无接触的群集为证书 autorollover 配置的初始部署。 下面提供了详细步骤。 我们将混合使用 PowerShell cmdlet 和 json 模板段。 与 Azure 交互的所有支持方法都可实现相同的功能。

[!NOTE] 此示例假设保管库中已经存在一个证书;注册和续订 KeyVault 托管证书需要必备的手动步骤，如本文前面所述。 对于生产环境，请使用 KeyVault-托管证书-下面提供了特定于 Microsoft 内部 PKI 的示例脚本。
证书 autorollover 仅适用于 CA 颁发的证书;使用自签名证书（包括在 Azure 门户中部署 Service Fabric 群集时生成的证书）是过程的，但仍可能用于本地/开发人员托管的部署，方法是将颁发者指纹声明为与叶证书相同。

### <a name="starting-point"></a>起点
为简洁起见，我们将假定以下开始状态：
  - Service Fabric 群集存在，并使用指纹声明的 CA 颁发的证书进行保护。
  - 此证书存储在保管库中，并预配为虚拟机规模集机密
  - 将使用相同的证书将群集转换为基于名称的常用证书声明，并可由主题和颁发者验证。如果不是这种情况，请获取旨在实现此目的的 CA 颁发的证书，并按[如下](service-fabric-cluster-security-update-certs-azure.md)所述通过指纹将其添加到群集定义中

下面是与此类状态相对应的模板的 json 摘录，这会省略许多必需的设置，并且仅阐释与证书相关的方面：
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

上述内容本质上指出，具有指纹```json [parameters('primaryClusterCertificateTP')] ```且在 KeyVault URI ```json [parameters('clusterCertificateUrlValue')] ```中找到的证书通过指纹声明为群集的唯一证书。 接下来，我们将设置所需的其他资源，以确保证书的 autorollover。

### <a name="setting-up-prerequisite-resources"></a>设置必备资源
如前所述，由 Microsoft. 计算资源提供程序服务从保管库中检索预配为虚拟机规模集机密的证书，并使用其第一方标识和代表部署操作员。 对于要更改的 autorollover，我们将切换到使用托管标识，分配给虚拟机规模集，并向其授予对保管库机密的权限。

所有后续摘录应部署 concomitantly，它们分别列出用于播放时分析和说明。

首先定义用户分配的标识（默认值作为示例包含）-有关最新信息，请参阅[官方文档](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity)：
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

然后授予此标识访问保管库机密的权限-请参阅[官方文档](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)了解最新信息：
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

在下一步中，我们将：
  - 将用户分配的标识分配给虚拟机规模集
  - 声明虚拟机规模集 "依赖于创建托管标识，并根据向其授予对保管库的访问权限" 的结果
  - 声明 KeyVault VM 扩展，要求它在启动时检索观察到的证书（[官方文档](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows)）
  - 更新 Service Fabric VM 扩展的定义，使其依赖于 KVVM 扩展，并将群集证书转换为公用名称（我们将在单个步骤中进行这些更改，因为它们属于同一资源的作用域。）

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
请注意，虽然上面未明确列出，但我们从虚拟机规模集的 "OsProfile" 部分中删除了保管库证书 URL。
最后一步是更新群集定义，将证书声明从指纹更改为公用名，此处也会固定颁发者指纹：

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

此时，你可以在单个部署中运行上述更新;在此过程中，Service Fabric 资源提供程序服务将按多个步骤拆分群集升级，如将[群集证书从指纹转换为公用名](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations)一段中所述。

### <a name="analysis-and-observations"></a>分析和观察
本部分介绍上述详细步骤，并对重要方面的注意力进行说明。

#### <a name="certificate-provisioning-explained"></a>证书预配，说明
作为预配代理的 KVVM 扩展以预定的频率连续运行。 如果无法检索观察到的证书，它将继续到下一步，直到下一个周期。 作为群集启动代理的 SFVM 扩展将需要声明的证书，然后群集才能形成。 这反过来意味着，SFVM 扩展只能在成功检索群集证书之后（在此处用```json "provisionAfterExtensions" : [ "KVVMExtension" ]"```子句表示，并由 KeyVaultVM 扩展的```json "requireInitialSync": true```设置运行）。 这表示首次运行时的 KVVM 扩展（在部署或重新启动后）必须循环通过其观察到的证书，直到全部下载成功。 如果将此参数设置为 "false"，则结合检索群集证书失败将导致群集部署失败。 相反，要求初始同步具有不正确/无效的观察到的证书列表会导致 KVVM 扩展失败，因此，无法部署群集。  

#### <a name="certificate-linking-explained"></a>证书链接，说明
您可能已注意到 KVVM 扩展的 "linkOnRenewal" 标志，这一事实已设置为 false。 这里，我们将深入探讨由此标志控制的行为及其对群集功能的影响。 请注意，此行为特定于 Windows。

根据其[定义](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows#extension-schema)：
```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

用于建立 TLS 连接的证书通常是通过 S 通道安全支持提供商[获取的句柄](https://docs.microsoft.com/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea)，也就是说，客户端不会直接访问证书本身的私钥。 S 通道支持证书扩展（[CERT_RENEWAL_PROP_ID](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)）形式的凭据的重定向（链接）：如果设置此属性，则其值表示 "续订" 证书的指纹，因此 S 通道将改为尝试加载链接的证书。 事实上，它会遍历此链接的（且无需循环的）列表，直到它最终使用 "最终" 证书（一个没有续订标记的证书）。 此功能在谨慎使用时，对因过期的证书（例如）而导致的可用性损失是一项很好的缓解措施。 在其他情况下，这可能导致难以诊断和缓解的中断。 S 通道无条件地在其续订属性上执行证书遍历，而不考虑使用者、颁发者或其他任何特定属性，这些属性参与客户端对生成的证书的验证。 实际上，生成的证书没有关联的私钥，或者该密钥尚未被列入到其预期使用者。 
 
如果启用了链接，则在从保管库检索观测到的证书时，KeyVault VM 扩展将尝试查找匹配的现有证书，以便通过续订扩展属性来链接它们。 匹配基于使用者备用名称（SAN），并按如下所示运行举例说明。
假设有两个现有证书，如下所示： A： CN = "Alice 的附件"，SAN = {"alice.universalexports.com"}，续订 = "" B： CN = "Bob 的位"，SAN = {"bob.universalexports.com"，"bob.universalexports.net"}，续订 = ""
 
假设证书 C 由 KVVM ext： CN = "Mallory"、SAN = {"alice.universalexports.com"、"bob.universalexports.com"、"mallory.universalexports.com"} 检索
 
A 的 SAN 列表已完全包含在 C 的中，因此续订 = c. 指纹;B 的 SAN 列表与 C 有一个共同的交集，但并不完全包含在其中，因此，b. 续订将保留为空。
 
在证书 A 上，在此状态下调用 AcquireCredentialsHandle （S 通道）的任何尝试都会实际最终将 C 发送到远程方。 在 Service Fabric 的情况下，群集的[传输子系统](service-fabric-architecture.md#transport-subsystem)使用 S 通道进行相互身份验证，因此上述行为直接影响群集的基本通信。 继续上面的示例，假设是群集证书，接下来会发生什么情况：
  - 如果 C 的私钥不会已纳入 acl 到作为构造运行的帐户，我们将看到获取私钥（SEC_E_UNKNOWN_CREDENTIALS 或类似项）失败
  - 如果 C 的私钥是可访问的，则会看到其他节点（CertificateNotMatched、未授权等）返回的授权失败。 
 
在任一情况下，传输都会失败，群集可能会关闭;症状有所不同。 为了使问题更糟，链接依赖于续订顺序–由 KVVM 扩展的观察证书列表的顺序决定，保管库中的续订计划甚至会改变检索顺序的暂时性错误。

若要缓解此类事件，我们建议：
  - 不要混合使用不同的保管库证书，每个保管库证书应提供不同的目的，并且它们的使用者和 SAN 应反映出相关情况
  - 在 SAN 列表中包括使用者公用名（按原义 "CN =<subject common name>"）  
  - 如果不确定，禁用通过 KVVM 扩展预配的证书的续订链接 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>为什么使用用户分配的托管标识？ 使用它有什么影响？
由于上述 json 代码段中所示，需要执行操作/更新的特定排序才能确保转换成功，以及维护群集的可用性。 具体而言，虚拟机规模集资源声明并使用其标识在单个（从用户的角度）更新中检索机密。 Service Fabric VM 扩展（引导群集）取决于是否已成功地检索观察到的证书。 KVVM 扩展使用虚拟机规模集的标识来访问保管库，这意味着在部署虚拟机规模集之前，保管库上的访问策略必须已更新。 

若要释放托管标识的创建，或将其分配给其他资源，部署运算符必须具有订阅或资源组中的必需角色（ManagedIdentityOperator），以及管理模板中引用的其他资源所需的角色。 

从安全角度来看，请记住，虚拟机（规模集）被视为与其 Azure 标识相关的安全边界。 这意味着，在 VM 上托管的任何应用程序都可以获取一个访问令牌，该令牌表示 VM 管理的标识访问令牌，这些令牌是从未经身份验证的 IMDS 终结点获取的。 如果将 VM 视为共享或多租户环境，则不会指示此检索群集证书的方法。 不过，它是适用于证书 autorollover 的唯一预配机制。

## <a name="troubleshooting-and-frequently-asked-questions"></a>故障排除和常见问题

*问*：如何以编程方式注册到 KeyVault 管理的证书？
*答*：从 KeyVault 文档中找出颁发者的名称，然后在下面的脚本中将其替换。  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*问*：未声明的或未指定的颁发者颁发证书时会发生什么情况？ 在哪里可以获取给定 PKI 的活动颁发者的详尽列表？
*答*：如果证书声明指定了颁发者指纹，而该证书的直接颁发者未包含在固定颁发者列表中，则该证书将被视为无效，无论其根是否受客户端信任。 因此，确保颁发者列表是最新的，这一点非常重要。 引入新的颁发者是一种极少的事件，应在开始颁发证书之前广泛公布。 

通常，根据 IETF [RFC 7382](https://tools.ietf.org/html/rfc7382)，PKI 将发布和维护认证实践声明。 此外，它还包括所有活动的颁发者。 以编程方式检索此列表可能与 PKI 不同。   

有关 Microsoft 内部 Pki 的详细说明，请参阅用于检索授权颁发者的终结点/Sdk 的内部文档。群集所有者负责定期探测此列表，并确保其群集定义包括*所有*预期的颁发者。

*问*：是否支持多个 pki？ 
*答*：是;你不能在群集清单中用相同的值声明多个 CN 项，但可以列出与同一 CN 对应的多个 Pki 中的颁发者。 这不是建议的做法，证书透明度实践可能会阻止颁发此类证书。 然而，作为从一个 PKI 迁移到另一个 PKI 的方法，这是一个可接受的机制。

*问*：如果当前群集证书不是由 CA 颁发的，或者没有所需的使用者，该怎么办？ 
*答*：使用目标主题获取证书，并按指纹将其作为次要证书添加到群集的定义。 升级成功完成后，启动另一次群集配置升级，将证书声明转换为公用名。 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png

