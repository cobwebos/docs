---
title: Service Fabric 群集中的证书管理
description: 了解如何在使用 X.509 证书保护的 Service Fabric 群集中管理证书。
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: aba681157d71f94914462b8d9fc13b90d4d6b153
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653658"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Service Fabric 群集中的证书管理

本文说明了如何管理用于保护 Azure Service Fabric 群集中的通信的证书，并且补充了对 [Service Fabric 群集安全性](service-fabric-cluster-security.md)的介绍，还对 [Service Fabric 中基于 X.509 证书的身份验证](cluster-security-certificates.md)进行了补充说明。 假设读者熟悉基本的安全性概念，以及 Service Fabric 公开的用于配置群集安全性的控制措施。  

本文涵盖以下方面：

* “证书管理”的具体涵义是什么？
* 证书管理中涉及的角色和实体
* 证书的历程
* 深入探讨一个示例
* 故障排除和常见问题解答

不过，首先是一个免责声明：本文尝试将理论与实践相结合，这就要求受众了解服务、技术等方面的细节。 由于就前往的一部分是 Microsoft 内部的，因此我们将介绍特定于 Microsoft Azure 的服务、技术和产品。 如果特定于 Microsoft 的细节不适用于你的情况，请在评论部分提问，以获得详细说明或相关指导。

## <a name="defining-certificate-management"></a>定义证书管理
正如我们在[配套文章](cluster-security-certificates.md)中看到的那样，证书是一个加密对象，它本质上是将某个非对称密钥对与描述它所表示的实体的属性绑定在一起。 但它也是一个“易变质”对象，原因在于它的生存期有限，并且容易泄漏 - 不管是意外泄露还是被攻击者成功利用，都可能会使证书变得毫无用处（从安全角度来看）。 这意味着需要更改证书 - 定期更改或为了响应安全事件而更改。 管理的另一方面（其本身就是一整个主题）是保护证书私钥，或者保护相关机密，这些机密保护那些涉及证书获得和预配过程的实体的标识。 我们将用于获取证书并将其安全地传输到需要证书的位置的流程和过程称为“证书管理”。 某些管理操作（例如注册、策略设置和授权控制）超出了本文的范围。 还有其他一些功能（如预配、续订、重新生成密钥或吊销）只是偶尔与 Service Fabric 相关。尽管如此，我们仍会在此对其进行某种程度的阐述，因为了解这些操作有助于用户正确保护其群集。 

目标是尽可能将证书管理自动化，以确保群集的不间断可用性并提供安全保证，因为此过程无需用户参与。 此目标目前在 Azure Service Fabric 群集中是可以达到的；本文其余部分将首先析构证书管理，然后重点介绍如何启用自动滚动更新。

具体而言，范围内的主题包括：
  - 与管理证书时将所有者和平台的属性分开相关的假设
  - 证书从颁发到使用这一过程的长管道
  - 证书轮换 - 原因、操作方法和时间
  - 哪些方面可能会出错？

保护/管理域名、注册到证书中，或者通过设置授权控制来强制颁发证书等方面的内容超出了本文的范围。 请访问你最喜欢的公钥基础结构 (PKI) 服务的注册机构 (RA)。 Microsoft 内部使用者：请联系 Azure 安全部门。

## <a name="roles-and-entities-involved-in-certificate-management"></a>证书管理中涉及的角色和实体
Service Fabric 群集中的安全方法是这样的：群集所有者声明它，Service Fabric 运行时强制执行它。 这意味着，几乎没有任何证书、密钥或其他参与群集功能的标识凭据来自服务本身；它们都由群集所有者声明。 此外，群集所有者还负责将证书预配到群集中、根据需要续订这些证书，并且始终确保证书的安全性。 更具体地说，群集所有者必须确保：
  - 在群集清单的 NodeType 节中声明的证书可以在该类型的每个节点上找到（根据[出示规则](cluster-security-certificates.md#presentation-rules)）
  - 上面声明的证书在安装时包含其相应的私钥
  - 在出示规则中声明的证书应能够通过[验证规则](cluster-security-certificates.md#validation-rules) 

Service Fabric 自身将承担以下职责：
  - 定位/查找与群集定义中的声明匹配的证书  
  - 根据“需要”向 Service Fabric 控制的实体授予相应私钥的访问权限
  - 严格按照已制定的安全性最佳做法和群集定义来验证证书
  - 在证书即将过期或无法执行基本的证书验证步骤时发出警报
  - （在一定程度上）验证主机的基础配置是否满足群集定义中与证书相关的方面的要求 

由此可见，证书管理负担（以活动操作的形式）完全落在群集所有者身上。 在以下部分，我们将详细介绍每个管理操作，以及可用机制及其对群集的影响。

## <a name="the-journey-of-a-certificate"></a>证书的历程
让我们快速回顾一下在 Service Fabric 群集环境中证书从颁发到使用的整个过程：

  1. 域所有者向 PKI 的 RA 注册要与继而生成的证书关联的域或使用者，而这些证书则构成对上述域或使用者的所有权证明
  2. 域所有者还在 RA 中指定了有权请求证书注册到指定域或使用者的授权请求方的标识;在 Microsoft Azure 中，默认标识提供者是 Azure Active Directory，授权的请求者由其相应的 AAD 标识 (或通过安全组指定) 
  3. 然后，已获授权的请求者通过密钥管理服务注册到证书;在 Microsoft Azure 中，所选的短信是 Azure Key Vault (AKV) ，它安全地存储和允许经授权的实体检索机密/证书。 AKV 还会根据已关联证书策略中的配置对证书执行续订/重新生成密钥操作。 （AKV 使用 AAD 作为标识提供者。）
  4. 经授权的检索器（我们称之为“预配代理”）会从保管库检索证书（包括其私钥），并将其安装在用于托管群集的计算机上
  5. Service Fabric 服务（在每个节点上以提升的权限运行）将证书访问权限授予那些获得允许的 Service Fabric 实体；这些实体由本地组指定，在 ServiceFabricAdministrators 和 ServiceFabricAllowedUsers 之间进行拆分
  6. Service Fabric 运行时会访问并使用证书来建立联合身份验证，或者对经过授权的客户端发出的入站请求进行身份验证
  7. 预配代理监视保管库证书，并在检测到续订时触发预配流；然后，群集所有者会根据需要更新群集定义，表明要滚动更新证书的意图。
  8. 预配代理或群集所有者还负责清除/删除未使用的证书
  
对于我们的目的而言，以上一系列步骤中的前两个步骤在很大程度上是不相关的；唯一的联系是，证书的使用者公用名称是在群集定义中声明的 DNS 名称。

这些步骤如下所示；请注意分别通过指纹和公用名称声明的证书在预配方面的差异。

图 1. 通过指纹声明的证书的颁发和预配流。
![预配通过指纹声明的证书][Image1]

图 2. 通过使用者公用名称声明的证书的颁发和预配流。
![预配通过所有者公用名称声明的证书][Image2]

### <a name="certificate-enrollment"></a>证书注册
有关本主题的详细介绍，请参阅 Key Vault [文档](../key-vault/certificates/create-certificate.md)；我们在此提供一个概要是为了保持连续性，并且也方便你参考。 在继续使用 Azure 的情况下，如果使用 Azure Key Vault 作为机密管理服务，则经授权的证书请求者必须至少在保管库上具有由保管库所有者授予的证书管理权限；然后，请求者会注册到证书中，如下所示：
    - 在 Azure Key Vault (AKV) 中创建一个证书策略，该策略指定证书的域/所有者、所需颁发者、密钥类型和长度、预期的密钥用法，等等；有关详细信息，请参阅 [Azure Key Vault 中的证书](../key-vault/certificates/certificate-scenarios.md)。 
    - 使用上面指定的策略在同一保管库中创建一个证书；该证书接下来会生成一个用作保管库对象的密钥对、一个使用私钥签名的证书签名请求，然后该请求会转发给指定的颁发者进行签名。
    - 颁发者（证书颁发机构）使用签名证书进行答复后，系统会将结果合并到保管库中，然后证书即可用于以下操作：
      - 在 {vaultUri}/certificates/{name} 下：包含公钥和元数据的证书
      - 在 {vaultUri}/keys/{name} 下：证书的私钥，可用于加密操作（包装/解包、签名/验证）
      - 在 {vaultUri}/secrets/{name} 下：包含私钥的证书，可作为不受保护的 pfx 或 pem 文件下载  
    请回想一下，保管库证书实际上是一系列按时间排序的共享某个策略的证书实例。 证书版本将根据策略的生存期和续订属性创建。 强烈建议不要让保管库证书共享使用者或域/DNS 名称；在一个群集中，若要从不同的保管库证书预配证书实例，并且这些证书的使用者相同，但其他属性（例如颁发者、密钥用法等）却存在实质性的差异，则可能会造成中断。

此时，保管库中存在一个可供使用的证书。 接下来讲述：

### <a name="certificate-provisioning"></a>证书预配
我们提到过“预配代理”，这是一个实体，用于从保管库检索证书（包括其私钥），并将其安装在群集的每个主机上。 （请回想一下，Service Fabric 不预配证书。）在我们的上下文中，群集会托管在一系列 Azure VM 和/或虚拟机规模集上。 在 Azure 中，可以使用以下机制将证书从保管库预配到 VM/VMSS - 如上所述，假设预配代理以前已被保管库所有者授予保管库的“获取”权限： 
  - 即席：操作员从保管库中检索证书（以 pfx/PKCS #12 或 pem 的形式），并将其安装在每个节点上
  - 在部署过程中作为虚拟机规模集“机密”：计算服务代表操作员使用第一方标识从一个启用了模板部署的保管库检索证书，然后将其安装在虚拟机规模集的每个节点上（[像这样](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)）；请注意，这只允许预配进行了版本控制的机密
  - 使用 [Key Vault VM 扩展](../virtual-machines/extensions/key-vault-windows.md)；这样可以使用无版本声明来预配证书，并可定期刷新观察到的证书。 在这种情况下，VM/VMSS 应该有一个[托管标识](../virtual-machines/security-policy.md#managed-identities-for-azure-resources)，该标识已被授予对观察到的证书所在的保管库的访问权限。

出于多种原因（包括从安全性到可用性在内的各种原因），不建议使用即席机制，我们在这里不对其进行进一步的讨论。有关详细信息，请参阅[虚拟机规模集中的证书](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)。

基于 VMSS/计算的预配具有安全性和可用性方面的优势，但也存在限制。 根据设计，它要求将证书声明为进行版本控制的机密，因此仅适用于通过指纹声明的证书所保护的群集。 与之相反，基于 Key Vault VM 扩展的预配会始终安装每个观察到的证书的最新版本，因此仅适用于通过使用者公用名称声明的证书所保护的群集。 需要强调的是，请不要对按实例（即按指纹）声明的证书使用自动刷新预配机制（例如 KVVM 扩展）- 失去可用性的风险很大。

可能存在其他预配机制；上面是目前接受的可用于 Azure Service Fabric 群集的机制。

### <a name="certificate-consumption-and-monitoring"></a>证书的使用和监视
如前所述，Service Fabric 运行时负责查找和使用在群集定义中声明的证书。 有关[基于证书的身份验证](cluster-security-certificates.md)的文章详细说明了 Service Fabric 分别如何实施出示和验证规则，在此不再讲述。 我们将探讨访问权限和权限的授予和监视。

请回想一下，Service Fabric 中的证书用于多种目的，从联合层中的相互身份验证到管理终结点的 TLS 身份验证，不一而足；这需要不同的组件或系统服务才能访问证书的私钥。 Service Fabric 运行时定期扫描证书存储，查找每个已知出示规则的匹配项；对于每个匹配的证书，会找到相应的私钥，并更新其自由访问控制列表，使其包含权限（通常为读取和执行权限），这些权限授予给需要它们的相应标识。 （此过程非正式地称为“ACL”。）此过程在 1 分钟的时间段内运行，并且还介绍了“应用程序”证书，例如用于加密设置的证书或终结点证书。 ACL 遵循出示规则，因此请务必记住：通过指纹声明的证书和未进行后续群集配置更新的自动刷新的证书将不可访问。    

### <a name="certificate-rotation"></a>证书轮换
注意：IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) 正式将[续订](https://tools.ietf.org/html/rfc3647#section-4.4.6)定义为使用与要替换的证书相同的属性来颁发证书 - 将会保留颁发者、使用者的公钥和信息，并会在颁发证书时使用新的密钥对来[重新生成密钥](https://tools.ietf.org/html/rfc3647#section-4.4.7)，不对是否可以更改颁发者进行限制。 考虑到其中的区别可能很重要（试想一下在颁发者固定的情况下通过使用者公用名称来声明证书），我们将选择中性术语“轮换”来涵盖这两种方案。 （请记住，在非正式使用的情况下，“续订”事实上指的是重新生成密钥。） 

之前我们曾见到 Azure Key Vault 支持自动证书轮换：关联的证书策略定义在保管库中轮换证书的时间点，不管在表示时采用的是过期前的天数还是总生存期的百分比。 必须在此时间点之后（现在的旧证书过期之前）调用预配代理，然后才能将此新证书分发到群集的所有节点。 如果证书的到期日期（当前在群集中使用）早于预先确定的时间间隔，Service Fabric 会协助发出运行状况警告。 配置为观察保管库证书的自动预配代理（即 KeyVault VM 扩展）会定期轮询保管库、检测轮换情况，以及检索并安装新证书。 通过 VM/VMSS 的“机密”功能进行预配时，需要一位经授权的操作员使用与新证书相对应的进行了版本控制的 KeyVault URI 来更新 VM/VMSS。

不管什么情况，轮换的证书现在都已预配到了所有节点，而我们也介绍了 Service Fabric 采用的用于检测轮换情况的机制；接下来，让我们看看会发生什么 - 假设轮换适用于通过使用者公用名称声明的群集证书（在撰写本文时全都适用，Service Fabric 的运行时版本为 7.1.409）：
  - 对于群集内部的新连接以及要发到群集中的新连接，Service Fabric 运行时都会找到并选择具有最远到期日期（证书的“NotAfter”属性，通常缩写为“na”）的匹配证书。
  - 现有连接将保持活动状态，或者获允以自然方式过期或终止；系统会通知内部处理程序：存在新的匹配项

这将转换为以下重要观察结果：
  - 如果续订证书的到期日期早于当前使用的证书的到期日期，系统会忽略续订证书。
  - 群集或已托管应用程序的可用性优先于要求轮换证书的指令；群集最终会聚合到新证书，但没有时间保证。 结果就是：
  - 在观察者看来，并不是立刻就能看到轮换的证书彻底取代其前身；若要确保这一点（对群集证书来说），唯一方法是重启主机。 请注意，重启 Service Fabric 节点是不够的，因为在群集中形成租约连接的内核模式组件不会受到影响。 另请注意，重启 VM/VMSS 可能会导致暂时丢失可用性。 （对于应用程序证书，只需重启各自的应用程序实例即可。）
  - 引入不符合验证规则的重新生成密钥的证书可能会实质性地破坏群集。 这方面最常见的示例是出现意外的颁发者：群集证书是在固定颁发者的情况下通过使用者公用名称声明的，但轮换的证书是由新的/未声明的颁发者颁发的。     

### <a name="certificate-cleanup"></a>证书清除
目前，Azure 中没有可以显式删除证书的预配。 通常情况下，很难确定是否在给定的时间使用了给定的证书。 相比于群集证书，应用程序证书更加难以进行上述确定。 Service Fabric 本身（而不是预配代理）在任何情况下都不会删除用户声明的证书。 对于标准预配机制来说，会出现以下情况：
  - 声明为 VM/VMSS 机密的证书会被预配，前提是这些证书在 VM/VMSS 定义中被引用，它们可以从保管库中检索（删除保管库机密/证书会导致后续 VM/VMSS 部署失败；同样，禁用保管库中的机密版本也会导致引用该机密版本的 VM/VMSS 部署失败）
  - 通过 KeyVault VM 扩展预配的先前版本的证书不一定存在于 VM/VMSS 节点上。 代理仅检索并安装当前版本，不删除任何证书。 重置节点映像（通常每月进行一次）会将证书存储重置为 OS 映像的内容，因此会隐式删除以前的版本。 但请考虑一下，纵向扩展虚拟机规模集将仅导致系统安装所观察证书的当前版本；在已安装证书的情况下，请勿采用节点同质化操作。   

## <a name="simplifying-management---an-autorollover-example"></a>简化管理 - 自动滚动更新示例
我们介绍了各种机制、限制，概述了复杂的规则和定义，并对中断进行了极端预测。 或许还需要说明如何设置自动证书管理，以避免所有这些问题。 我们会在 Azure Service Fabric 群集（在 PaaSv2 虚拟机规模集上运行）的上下文中执行此操作，使用 Azure Key Vault 进行机密管理，并利用托管标识，如下所示：
  - 证书的验证从指纹固定更改为使用者 + 颁发者固定：给定颁发者颁发的具有给定使用者的任何证书都会受到相同的信任
    - 证书的注册和获取在受信任的存储 (Key Vault) 中进行，并由代理（在本例中为 KeyVault VM 扩展）进行刷新
    - 证书的预配不再在部署时进行，也不再基于版本（像 ComputeRP 那样），而是在部署后进行，并且使用无版本的 KeyVault URI
    - 通过用户分配的托管标识授予对 KeyVault 的访问权限；在部署过程中，将创建 UA 标识并将其分配给虚拟机规模集
    - 部署后，代理（KV VM 扩展）会轮询并刷新在虚拟机规模集的每个节点上观察到的证书；因此，证书轮换是完全自动的，因为 SF 会自动选取最远的有效证书

此序列可以完全脚本化/自动化，可以在不让用户接触的情况下对某个已配置为证书自动滚动更新的群集进行初始部署。 下面提供了详细步骤。 我们将混合使用 PowerShell cmdlet 和 json 模板片段。 可以使用与 Azure 交互时所有支持的方法来实现相同的功能。

[!NOTE] 此示例假设保管库中已经存在一个证书；注册和续订 KeyVault 托管证书需要完成充当先决条件的手动步骤，如本文前面所述。 对于生产环境，请使用 KeyVault 托管证书 - 下面提供了特定于 Microsoft 内部 PKI 的示例脚本。
证书自动滚动更新仅适用于 CA 颁发的证书；使用自签名证书（包括在 Azure 门户中部署 Service Fabric 群集时生成的证书）是不合理的，但自签名证书仍可用于本地/开发人员托管的部署，方法是将颁发者指纹声明为与叶证书的相同。

### <a name="starting-point"></a>起点
为了简单起见，我们将假定采用以下开始状态：
  - Service Fabric 群集存在，并使用通过指纹声明的 CA 颁发的证书进行保护。
  - 此证书存储在保管库中，并预配为虚拟机规模集机密
  - 将使用同一证书将群集转换为基于常用名称的证书声明，因此可以通过所有者和颁发者进行验证；如果不是这种情况，请获取用于此目的的 CA 颁发的证书，并按指纹将其添加到群集定义中，如[此处](service-fabric-cluster-security-update-certs-azure.md)介绍的那样

下面是与此类状态相对应的模板的 json 摘录 - 注意，这里省略了许多必需的设置，仅演示了与证书相关的方面：
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

上面的内容本质上是说明：具有指纹 ```json [parameters('primaryClusterCertificateTP')] ``` 且在 KeyVault URI ```json [parameters('clusterCertificateUrlValue')] ``` 中找到的证书已按指纹声明为群集的唯一证书。 接下来，我们将设置所需的其他资源，以确保证书的自动滚动更新。

### <a name="setting-up-prerequisite-resources"></a>设置必备资源
如前所述，由 Microsoft.Compute 资源提供程序服务代表部署操作员使用其第一方标识从保管库中检索已预配为虚拟机规模集机密的证书。 对于自动滚动更新，该操作需更改 - 我们将改用分配给虚拟机规模集的托管标识，该标识被授予对保管库机密的访问权限。

所有后续摘录应进行伴随部署 - 它们单个列出，可以以逐个播放的方式进行分析和说明。

首先定义用户分配的标识（默认值作为示例包括）- 有关最新信息，请参阅[官方文档](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)：
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

然后授予此标识访问保管库机密的权限 - 有关最新信息，请参阅[官方文档](/rest/api/keyvault/vaults/updateaccesspolicy)：
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

在下一步中，我们将执行以下操作：
  - 将用户分配的标识分配给虚拟机规模集
  - 在创建托管标识并向其授予对保管库的访问权限后声明虚拟机规模集的依赖项
  - 声明 KeyVault VM 扩展，要求它在启动时检索观察到的证书（[官方文档](../virtual-machines/extensions/key-vault-windows.md)）
  - 更新 Service Fabric VM 扩展的定义，使其依赖于 KVVM 扩展，并将群集证书转换为公用名称（我们将在单个步骤中进行这些更改，因为它们属于同一资源的范围。）

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
请注意，我们已从虚拟机规模集的“OsProfile”部分中删除了保管库证书 URL（虽然上面未明确列出）。
最后一步是更新群集定义，将证书声明从指纹更改为公用名称 - 此处也会固定颁发者指纹：

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

此时，你可以在单个部署中运行上述更新；Service Fabric 资源提供程序服务本身将按几个步骤拆分群集升级，如[将群集证书从指纹转换为公用名称](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations)中所述。

### <a name="analysis-and-observations"></a>分析和观察
本部分全方位概述了上述详细步骤，并让用户注意重要方面。

#### <a name="certificate-provisioning-explained"></a>证书预配（已介绍）
作为预配代理的 KVVM 扩展以预定的频率连续运行。 如果无法检索观察到的证书，则会转到行中的下一目标，然后休眠至下一周期。 作为群集启动代理的 SFVM 扩展将需要声明的证书，否则群集无法形成。 这反过来意味着，SFVM 扩展只能在成功检索群集证书之后运行，在此处由 ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` 子句表示，以及由 KeyVaultVM 扩展的 ```json "requireInitialSync": true``` 设置表示。 这是向 KVVM 扩展指示：首次运行时（在部署或重启后）必须循环访问其观察到的证书，直到全部下载成功。 如果将此参数设置为“false”，则在检索群集证书失败的情况下，群集部署也会失败。 相反，如果在要求初始同步时所使用的已观察到的证书的列表不正确/无效，则会导致 KVVM 扩展失败，因此会再次导致群集部署失败。  

#### <a name="certificate-linking-explained"></a>证书链接（已介绍）
你可能已注意到 KVVM 扩展的“linkOnRenewal”标志，以及它已设置为 false 这一事实。 在这里，我们将深入探讨此标志控制的行为及其对群集功能的影响。 请注意，此行为特定于 Windows。

根据其[定义](../virtual-machines/extensions/key-vault-windows.md#extension-schema)：
```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

用于建立 TLS 连接的证书通常是通过 S 通道安全支持提供商 [获取的句柄](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) ，也就是说，客户端不会直接访问证书本身的私钥。 S 通道支持重定向 (以证书扩展的形式链接) 凭据 ([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)) ：如果设置此属性，则其值表示 "续订" 证书的指纹，因此，S 通道将改为尝试加载链接的证书。 事实上，它会遍历此链接的 (，并希望以非循环的方式) 列表，直到它最终成为 "最终" 证书（一个无续订标记）。 此功能在谨慎使用的情况下，对因证书过期（此处为举例）而导致的可用性损失是一项很好的缓解措施。 在其他情况下，它可能导致难以诊断和缓解的中断。 S 通道无条件地在其续订属性上执行证书遍历，而不考虑使用者、颁发者或其他任何特定属性，这些属性参与客户端生成的证书的验证。 实际上，可能是生成的证书没有关联的私钥，或者该密钥尚未被 ACL 到其预期使用者。 
 
如果启用了链接，则在从保管库检索观测到的证书时，KeyVault VM 扩展会尝试查找匹配的现有证书，以便通过续订扩展属性来链接它们。 匹配完全基于使用者可选名称 (SAN)，其原理如下所示。
假设有两个现有证书，如下所示： A： CN = "Alice 的附件"，SAN = {"alice.universalexports.com"}，续订 = "" B： CN = "Bob 的位"，SAN = {"bob.universalexports.com"，"bob.universalexports.net"}，续订 = ""
 
假设证书 C 由 KVVM ext： CN = "Mallory"、SAN = {"alice.universalexports.com"、"bob.universalexports.com"、"mallory.universalexports.com"} 检索
 
A 的 SAN 列表已完全包含在 C 的中，因此续订 = c. 指纹;B 的 SAN 列表与 C 有一个共同的交集，但并不完全包含在其中，因此，b. 续订将保留为空。
 
在证书 A 上，在此状态下调用 AcquireCredentialsHandle（S 通道）的任何尝试实际上最终都会将 C 发送到远程方。 在 Service Fabric 的情况下，群集的 [传输子系统](service-fabric-architecture.md#transport-subsystem) 使用 S 通道进行相互身份验证，因此上述行为直接影响群集的基本通信。 让我们继续上面的示例，假设 A 是群集证书，接下来发生的情况取决于：
  - 如果 C 的私钥不已纳入 acl 结构正在运行的帐户，则 SEC_E_UNKNOWN_CREDENTIALS 或类似) 获取私钥 (会出现故障
  - 如果 C 的私钥是可访问的，则会看到其他节点返回的授权失败 (CertificateNotMatched、未授权等 )  
 
在任一情况下，传输都会失败，群集可能会关闭；症状各不相同。 为了使问题更糟，链接依赖于续订顺序–由 KVVM 扩展的观察证书列表的顺序决定，保管库中的续订计划甚至会改变检索顺序的暂时性错误。

若要缓解此类事件，我们建议你采取以下措施：
  - 不要混合使用不同保管库证书的 SAN，每个保管库证书应具有不同的用途，且其使用者和 SAN 应通过特异性反映出相关情况
  - 在 SAN 列表中包括使用者公用名称（按原义，即 "CN =<subject common name>"）  
  - 在不确定的情况下，禁用通过 KVVM 扩展预配的证书的续订链接 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>为什么使用用户分配的托管标识？ 使用它会产生什么影响？
如图上述 json 代码段中出现的一样，需要对操作/更新进行特定的排序才能确保转换成功并维护群集的可用性。 具体而言，虚拟机规模集资源会声明并使用其标识在单个（从用户的角度来看）更新中检索机密。 Service Fabric VM 扩展（用于引导群集）取决于是否已完成 KeyVault VM 扩展，而后者依赖于是否已成功检索观察到的证书。 KVVM 扩展使用虚拟机规模集的标识来访问保管库，这意味着在部署虚拟机规模集之前，保管库上的访问策略必须已更新。 

若要释放托管标识的创建操作或将其分配给其他资源，部署操作员必须具有订阅或资源组中的必需角色 (ManagedIdentityOperator)，以及管理模板中引用的其他资源所需的角色。 

请回想一下，从安全角度来看，可以将虚拟机（规模集）视为与其 Azure 标识相关的安全边界。 这意味着，在 VM 上托管的任何应用程序原则上都可以获取一个表示 VM 的访问令牌 - 托管标识访问令牌是从未经身份验证的 IMDS 终结点获取的。 如果将 VM 视为共享的或多租户的环境，则可能不会指示此检索群集证书的方法。 不过，它是适用于证书自动滚动更新的唯一预配机制。

## <a name="troubleshooting-and-frequently-asked-questions"></a>故障排除和常见问题解答

*问*：如何以编程方式注册到 KeyVault 托管的证书？
*答*：找到 KeyVault 文档中的颁发者名称，然后在下面的脚本中将其替换。  
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

*问*：如果证书是由未声明/未指定的颁发者颁发的，会发生什么情况？ 在哪里可以获取给定 PKI 的活动颁发者的详尽列表？
*答*：如果证书声明指定了颁发者指纹，并且该证书的直接颁发者未包含在固定颁发者列表中，则该证书会被视为无效，无论其根是否受客户端信任。 因此，必须确保颁发者列表是最新的，这一点非常重要。 引入新的颁发者是一种罕见事件，应在开始颁发证书之前广而告之。 

通常情况下，根据 IETF [RFC 7382](https://tools.ietf.org/html/rfc7382) 的要求，PKI 会发布和维护认证实践声明。 此外，它还包括所有活动的颁发者。 以编程方式检索此列表时，一个 PKI 可能不同于另一个 PKI。   

关于 Microsoft 内部 PKI，请参阅用于检索经授权的颁发者的终结点/SDK 的相关内部文档；群集所有者负责定期探测此列表，确保其群集定义包括所有预期颁发者。

*问*：是否支持多个 PKI？ 
*答*：是的；你不能在群集清单中用相同的值声明多个 CN 项，但可以列出与同一 CN 对应的多个 PKI 中的颁发者。 建议不要这样做，证书透明度规范可能会阻止颁发此类证书。 然而，这是从一个 PKI 迁移到另一个 PKI 的方法，是一种可接受的机制。

*问*：如果当前群集证书不是由 CA 颁发的，或者没有所需的主题，该怎么办？ 
*答*：使用预期的使用者获取证书，并根据指纹将其作为次要证书添加到群集的定义中。 升级成功完成后，请启动另一次群集配置升级，将证书声明转换为公用名称。 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
