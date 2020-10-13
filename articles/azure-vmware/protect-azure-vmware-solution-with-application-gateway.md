---
title: 使用 Azure 应用程序网关保护 Azure VMware 解决方案上的 web 应用
description: 配置 Azure 应用程序网关，以安全地公开在 Azure VMware 解决方案上运行的 web 应用。
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: b63b7348419500504cb45917d741cc17e2b8a622
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951445"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>使用 Azure 应用程序网关保护 Azure VMware 解决方案上的 web 应用

[Azure 应用程序网关](https://azure.microsoft.com/services/application-gateway/) 是第7层 web 流量负载均衡器，可用于管理 web 应用程序的流量。 它提供多种功能：基于 cookie 的会话相关性、基于 URL 的路由，以及 Web 应用程序防火墙 (WAF) 。 有关功能的完整列表的 (，请参阅 [Azure 应用程序的网关功能](../application-gateway/features.md)。 ) 提供两个版本： v1 和 v2。 这两者都已在 Azure VMware 解决方案上运行的 web 应用中进行了测试。

在本文中，我们将逐步介绍使用应用程序网关的 web 服务器场的常见方案，其中包含一组用于保护在 Azure VMware 解决方案上运行的 web 应用的配置和建议。 

## <a name="topology"></a>拓扑
如下图所示，应用程序网关可用于保护 Azure IaaS 虚拟机、Azure 虚拟机规模集或本地服务器。 Azure VMware 解决方案虚拟机将由应用程序网关视为本地服务器。

![应用程序网关可保护 Azure VMware 解决方案 Vm。](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> 目前，Azure 应用程序网关是公开 Azure VMware 解决方案虚拟机上运行的 web 应用的唯一受支持的方法。

下图显示了用于通过 Azure VMware 解决方案 web 应用程序验证应用程序网关的测试方案。

![应用程序网关与运行 web 应用的 Azure VMware 解决方案集成。](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-avs-scenario.png)

应用程序网关实例部署在中心的专用子网中。 它具有 Azure 公共 IP 地址;建议为虚拟网络激活标准 DDoS 保护。 Web 服务器托管在 NSX T0 和 T1 路由器后面的 Azure VMware 解决方案私有云上。 Azure VMware 解决方案使用 [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) 来实现与集线器和本地系统的通信。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。
- 部署并运行的 Azure VMware 解决方案私有云。

## <a name="deployment-and-configuration"></a>部署和配置

1. 在 Azure 门户中，搜索 " **应用程序网关** "，然后选择 " **创建应用程序网关**"。

2. 提供基本详细信息，如下图所示：然后选择 " **下一步：前端>**。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="应用程序网关创建&quot;:::

3. 选择前端 IP 地址类型。 对于 &quot;公用&quot;，请选择现有的公共 IP 地址，或创建一个新的 IP 地址。 选择 **下一步：后端>**。

    > [!NOTE]
    > 专用前端仅支持标准和 Web 应用程序防火墙 (WAF) Sku。

4. 接下来，添加一个后端池，该池描述作为应用程序或服务一部分的一组实例，在这种情况下，在 Azure VMware 解决方案基础结构) 上运行的虚拟机 (。 提供 Azure VMware 解决方案私有云上运行的 web 服务器的详细信息，并选择 &quot; **添加**&quot;。然后选择 &quot; **下一步：配置>**。

1. 在 &quot; **配置** &quot; 选项卡上，选择 &quot; **添加路由规则**&quot;。

6. 在 &quot; **侦听器** &quot; 选项卡上，提供侦听器的详细信息。 如果选择了 HTTPS，则必须提供证书，无论是从 PFX 文件还是从 Azure Key Vault 中的现有证书。 

7. 选择 &quot; **后端目标** &quot; 选项卡，然后选择以前创建的后端池。 对于 &quot; **HTTP 设置** &quot; 字段，请选择 &quot; **新增**&quot;。

8. 配置 HTTP 设置的参数。 选择 **添加** 。

9. 如果要配置基于路径的规则，请选择 &quot; **添加多个目标&quot; 以创建基于路径的规则**。 

10. 添加基于路径的规则，然后选择 &quot; **添加**&quot;。 重复此步骤以添加其他基于路径的规则。 

11. 添加完基于路径的规则后，请再次选择 &quot; **添加** &quot;;然后选择 &quot; **下一步：标记&quot;>**。 

12. 添加任何所需的标记。 选择 &quot; **下一步&quot;：查看 + 创建>**。

13. 验证将在应用程序网关上运行;如果成功，请选择 &quot; **创建** " 以进行部署。

## <a name="configuration-examples"></a>配置示例

在本部分中，你将了解如何使用 Azure VMware 解决方案虚拟机将应用程序网关配置为以下用例的后端池： 

- [托管多个站点](#hosting-multiple-sites)
- [按 URL 路由](#routing-by-url)

### <a name="hosting-multiple-sites"></a>托管多个站点

创建应用程序网关时，可以使用 Azure 门户配置托管多个网站。 在本教程中，将使用现有应用程序网关上的 Azure VMware 解决方案私有云上运行的虚拟机定义后端地址池。 应用程序网关是中心虚拟网络的一部分，如在 [中心辐射型体系结构中集成 Azure VMware 解决方案](concepts-hub-and-spoke.md)中所述。 本教程假定你拥有多个域，并使用 www.contoso.com 和 www.fabrikam.com 的示例。

1. 创建虚拟机。 在 Azure VMware 解决方案私有云上，创建两个不同的虚拟机池：一个将代表 Contoso 和第二个 Fabrikam。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs.png" alt-text="应用程序网关创建&quot;:::

3. 选择前端 IP 地址类型。 对于 &quot;公用&quot;，请选择现有的公共 IP 地址，或创建一个新的 IP 地址。 选择 **下一步：后端>**。

    > [!NOTE]
    > 专用前端仅支持标准和 Web 应用程序防火墙 (WAF) Sku。

4. 接下来，添加一个后端池，该池描述作为应用程序或服务一部分的一组实例，在这种情况下，在 Azure VMware 解决方案基础结构) 上运行的虚拟机 (。 提供 Azure VMware 解决方案私有云上运行的 web 服务器的详细信息，并选择 &quot; **添加**&quot;。然后选择 &quot; **下一步：配置>**。

1. 在 &quot; **配置** &quot; 选项卡上，选择 &quot; **添加路由规则**&quot;。

6. 在 &quot; **侦听器** &quot; 选项卡上，提供侦听器的详细信息。 如果选择了 HTTPS，则必须提供证书，无论是从 PFX 文件还是从 Azure Key Vault 中的现有证书。 

7. 选择 &quot; **后端目标** &quot; 选项卡，然后选择以前创建的后端池。 对于 &quot; **HTTP 设置** &quot; 字段，请选择 &quot; **新增**&quot;。

8. 配置 HTTP 设置的参数。 选择 **添加** 。

9. 如果要配置基于路径的规则，请选择 &quot; **添加多个目标&quot; 以创建基于路径的规则**。 

10. 添加基于路径的规则，然后选择 &quot; **添加**&quot;。 重复此步骤以添加其他基于路径的规则。 

11. 添加完基于路径的规则后，请再次选择 &quot; **添加** &quot;;然后选择 &quot; **下一步：标记&quot;>**。 

12. 添加任何所需的标记。 选择 &quot; **下一步&quot;：查看 + 创建>**。

13. 验证将在应用程序网关上运行;如果成功，请选择 &quot; **创建** " **添加** "。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png" alt-text="应用程序网关创建&quot;:::

3. 选择前端 IP 地址类型。 对于 &quot;公用&quot;，请选择现有的公共 IP 地址，或创建一个新的 IP 地址。 选择 **下一步：后端>**。

    > [!NOTE]
    > 专用前端仅支持标准和 Web 应用程序防火墙 (WAF) Sku。

4. 接下来，添加一个后端池，该池描述作为应用程序或服务一部分的一组实例，在这种情况下，在 Azure VMware 解决方案基础结构) 上运行的虚拟机 (。 提供 Azure VMware 解决方案私有云上运行的 web 服务器的详细信息，并选择 &quot; **添加**&quot;。然后选择 &quot; **下一步：配置>**。

1. 在 &quot; **配置** &quot; 选项卡上，选择 &quot; **添加路由规则**&quot;。

6. 在 &quot; **侦听器** &quot; 选项卡上，提供侦听器的详细信息。 如果选择了 HTTPS，则必须提供证书，无论是从 PFX 文件还是从 Azure Key Vault 中的现有证书。 

7. 选择 &quot; **后端目标** &quot; 选项卡，然后选择以前创建的后端池。 对于 &quot; **HTTP 设置** &quot; 字段，请选择 &quot; **新增**&quot;。

8. 配置 HTTP 设置的参数。 选择 **添加** 。

9. 如果要配置基于路径的规则，请选择 &quot; **添加多个目标&quot; 以创建基于路径的规则**。 

10. 添加基于路径的规则，然后选择 &quot; **添加**&quot;。 重复此步骤以添加其他基于路径的规则。 

11. 添加完基于路径的规则后，请再次选择 &quot; **添加** &quot;;然后选择 &quot; **下一步：标记&quot;>**。 

12. 添加任何所需的标记。 选择 &quot; **下一步&quot;：查看 + 创建>**。

13. 验证将在应用程序网关上运行;如果成功，请选择 &quot; **创建** " lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png":::

3. 在 " **侦听器** " 部分中，为每个网站创建一个新的侦听器。 输入每个侦听器的详细信息，然后选择 " **添加**"。

4. 在左侧导航栏中，选择 " **HTTP 设置** "，并在左窗格中选择 " **添加** "。 填写详细信息以创建新的 HTTP 设置，然后选择 " **保存**"。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png" alt-text="应用程序网关创建&quot;:::

3. 选择前端 IP 地址类型。 对于 &quot;公用&quot;，请选择现有的公共 IP 地址，或创建一个新的 IP 地址。 选择 **下一步：后端>**。

    > [!NOTE]
    > 专用前端仅支持标准和 Web 应用程序防火墙 (WAF) Sku。

4. 接下来，添加一个后端池，该池描述作为应用程序或服务一部分的一组实例，在这种情况下，在 Azure VMware 解决方案基础结构) 上运行的虚拟机 (。 提供 Azure VMware 解决方案私有云上运行的 web 服务器的详细信息，并选择 &quot; **添加**&quot;。然后选择 &quot; **下一步：配置>**。

1. 在 &quot; **配置** &quot; 选项卡上，选择 &quot; **添加路由规则**&quot;。

6. 在 &quot; **侦听器** &quot; 选项卡上，提供侦听器的详细信息。 如果选择了 HTTPS，则必须提供证书，无论是从 PFX 文件还是从 Azure Key Vault 中的现有证书。 

7. 选择 &quot; **后端目标** &quot; 选项卡，然后选择以前创建的后端池。 对于 &quot; **HTTP 设置** &quot; 字段，请选择 &quot; **新增**&quot;。

8. 配置 HTTP 设置的参数。 选择 **添加** 。

9. 如果要配置基于路径的规则，请选择 &quot; **添加多个目标&quot; 以创建基于路径的规则**。 

10. 添加基于路径的规则，然后选择 &quot; **添加**&quot;。 重复此步骤以添加其他基于路径的规则。 

11. 添加完基于路径的规则后，请再次选择 &quot; **添加** &quot;;然后选择 &quot; **下一步：标记&quot;>**。 

12. 添加任何所需的标记。 选择 &quot; **下一步&quot;：查看 + 创建>**。

13. 验证将在应用程序网关上运行;如果成功，请选择 &quot; **创建** " lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png":::

5. 在左侧菜单的 " **规则** " 部分中创建规则。 将每个规则与相应的侦听器相关联。 选择 **添加** 。

6. 配置相应的后端池和 HTTP 设置。 选择 **添加** 。

7. 测试连接。 打开首选浏览器并导航到 Azure VMware 解决方案环境中托管的不同网站，例如 http://www.fabrikam.com 。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-07.png" alt-text="应用程序网关创建&quot;:::

3. 选择前端 IP 地址类型。 对于 &quot;公用&quot;，请选择现有的公共 IP 地址，或创建一个新的 IP 地址。 选择 **下一步：后端>**。

    > [!NOTE]
    > 专用前端仅支持标准和 Web 应用程序防火墙 (WAF) Sku。

4. 接下来，添加一个后端池，该池描述作为应用程序或服务一部分的一组实例，在这种情况下，在 Azure VMware 解决方案基础结构) 上运行的虚拟机 (。 提供 Azure VMware 解决方案私有云上运行的 web 服务器的详细信息，并选择 &quot; **添加**&quot;。然后选择 &quot; **下一步：配置>**。

1. 在 &quot; **配置** &quot; 选项卡上，选择 &quot; **添加路由规则**&quot;。

6. 在 &quot; **侦听器** &quot; 选项卡上，提供侦听器的详细信息。 如果选择了 HTTPS，则必须提供证书，无论是从 PFX 文件还是从 Azure Key Vault 中的现有证书。 

7. 选择 &quot; **后端目标** &quot; 选项卡，然后选择以前创建的后端池。 对于 &quot; **HTTP 设置** &quot; 字段，请选择 &quot; **新增**&quot;。

8. 配置 HTTP 设置的参数。 选择 **添加** 。

9. 如果要配置基于路径的规则，请选择 &quot; **添加多个目标&quot; 以创建基于路径的规则**。 

10. 添加基于路径的规则，然后选择 &quot; **添加**&quot;。 重复此步骤以添加其他基于路径的规则。 

11. 添加完基于路径的规则后，请再次选择 &quot; **添加** &quot;;然后选择 &quot; **下一步：标记&quot;>**。 

12. 添加任何所需的标记。 选择 &quot; **下一步&quot;：查看 + 创建>**。

13. 验证将在应用程序网关上运行;如果成功，请选择 &quot; **创建** ":::

### <a name="routing-by-url"></a>按 URL 路由

你可以使用 Azure 应用程序网关配置基于 URL 路径的路由规则。 在本教程中，将使用现有应用程序网关上的 Azure VMware 解决方案私有云上运行的虚拟机定义后端地址池。 应用程序网关是中心虚拟网络的一部分，如 [Azure VMware 解决方案 Azure 本机集成文档](concepts-hub-and-spoke.md)中所述。 然后创建路由规则，以确保 Web 流量到达池中的相应服务器。

1. 创建虚拟机。 在 Azure VMware 解决方案私有云上，创建虚拟机池来表示 web 场。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs.png" alt-text="应用程序网关创建&quot;:::

3. 选择前端 IP 地址类型。 对于 &quot;公用&quot;，请选择现有的公共 IP 地址，或创建一个新的 IP 地址。 选择 **下一步：后端>**。

    > [!NOTE]
    > 专用前端仅支持标准和 Web 应用程序防火墙 (WAF) Sku。

4. 接下来，添加一个后端池，该池描述作为应用程序或服务一部分的一组实例，在这种情况下，在 Azure VMware 解决方案基础结构) 上运行的虚拟机 (。 提供 Azure VMware 解决方案私有云上运行的 web 服务器的详细信息，并选择 &quot; **添加**&quot;。然后选择 &quot; **下一步：配置>**。

1. 在 &quot; **配置** &quot; 选项卡上，选择 &quot; **添加路由规则**&quot;。

6. 在 &quot; **侦听器** &quot; 选项卡上，提供侦听器的详细信息。 如果选择了 HTTPS，则必须提供证书，无论是从 PFX 文件还是从 Azure Key Vault 中的现有证书。 

7. 选择 &quot; **后端目标** &quot; 选项卡，然后选择以前创建的后端池。 对于 &quot; **HTTP 设置** &quot; 字段，请选择 &quot; **新增**&quot;。

8. 配置 HTTP 设置的参数。 选择 **添加** 。

9. 如果要配置基于路径的规则，请选择 &quot; **添加多个目标&quot; 以创建基于路径的规则**。 

10. 添加基于路径的规则，然后选择 &quot; **添加**&quot;。 重复此步骤以添加其他基于路径的规则。 

11. 添加完基于路径的规则后，请再次选择 &quot; **添加** &quot;;然后选择 &quot; **下一步：标记&quot;>**。 

12. 添加任何所需的标记。 选择 &quot; **下一步&quot;：查看 + 创建>**。

13. 验证将在应用程序网关上运行;如果成功，请选择 &quot; **创建** " **添加** "，然后输入第一个池（ **contoso-web**）的详细信息。 将一个 VM 添加为目标。 选择 **添加** 。 为 **contoso-images** 和 **contoso-视频**重复此过程，并将一个唯一 VM 添加为目标。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png" alt-text="应用程序网关创建&quot;:::

3. 选择前端 IP 地址类型。 对于 &quot;公用&quot;，请选择现有的公共 IP 地址，或创建一个新的 IP 地址。 选择 **下一步：后端>**。

    > [!NOTE]
    > 专用前端仅支持标准和 Web 应用程序防火墙 (WAF) Sku。

4. 接下来，添加一个后端池，该池描述作为应用程序或服务一部分的一组实例，在这种情况下，在 Azure VMware 解决方案基础结构) 上运行的虚拟机 (。 提供 Azure VMware 解决方案私有云上运行的 web 服务器的详细信息，并选择 &quot; **添加**&quot;。然后选择 &quot; **下一步：配置>**。

1. 在 &quot; **配置** &quot; 选项卡上，选择 &quot; **添加路由规则**&quot;。

6. 在 &quot; **侦听器** &quot; 选项卡上，提供侦听器的详细信息。 如果选择了 HTTPS，则必须提供证书，无论是从 PFX 文件还是从 Azure Key Vault 中的现有证书。 

7. 选择 &quot; **后端目标** &quot; 选项卡，然后选择以前创建的后端池。 对于 &quot; **HTTP 设置** &quot; 字段，请选择 &quot; **新增**&quot;。

8. 配置 HTTP 设置的参数。 选择 **添加** 。

9. 如果要配置基于路径的规则，请选择 &quot; **添加多个目标&quot; 以创建基于路径的规则**。 

10. 添加基于路径的规则，然后选择 &quot; **添加**&quot;。 重复此步骤以添加其他基于路径的规则。 

11. 添加完基于路径的规则后，请再次选择 &quot; **添加** &quot;;然后选择 &quot; **下一步：标记&quot;>**。 

12. 添加任何所需的标记。 选择 &quot; **下一步&quot;：查看 + 创建>**。

13. 验证将在应用程序网关上运行;如果成功，请选择 &quot; **创建** " lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png":::

3. 在 " **侦听器** " 部分中，使用端口8080创建一个类型为 "基本" 的新侦听器。

4. 在左侧导航栏中，选择 " **HTTP 设置** "，并在左窗格中选择 " **添加** "。 填写详细信息以创建新的 HTTP 设置，然后选择 " **保存**"。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-04.png" alt-text="应用程序网关创建&quot;:::

3. 选择前端 IP 地址类型。 对于 &quot;公用&quot;，请选择现有的公共 IP 地址，或创建一个新的 IP 地址。 选择 **下一步：后端>**。

    > [!NOTE]
    > 专用前端仅支持标准和 Web 应用程序防火墙 (WAF) Sku。

4. 接下来，添加一个后端池，该池描述作为应用程序或服务一部分的一组实例，在这种情况下，在 Azure VMware 解决方案基础结构) 上运行的虚拟机 (。 提供 Azure VMware 解决方案私有云上运行的 web 服务器的详细信息，并选择 &quot; **添加**&quot;。然后选择 &quot; **下一步：配置>**。

1. 在 &quot; **配置** &quot; 选项卡上，选择 &quot; **添加路由规则**&quot;。

6. 在 &quot; **侦听器** &quot; 选项卡上，提供侦听器的详细信息。 如果选择了 HTTPS，则必须提供证书，无论是从 PFX 文件还是从 Azure Key Vault 中的现有证书。 

7. 选择 &quot; **后端目标** &quot; 选项卡，然后选择以前创建的后端池。 对于 &quot; **HTTP 设置** &quot; 字段，请选择 &quot; **新增**&quot;。

8. 配置 HTTP 设置的参数。 选择 **添加** 。

9. 如果要配置基于路径的规则，请选择 &quot; **添加多个目标&quot; 以创建基于路径的规则**。 

10. 添加基于路径的规则，然后选择 &quot; **添加**&quot;。 重复此步骤以添加其他基于路径的规则。 

11. 添加完基于路径的规则后，请再次选择 &quot; **添加** &quot;;然后选择 &quot; **下一步：标记&quot;>**。 

12. 添加任何所需的标记。 选择 &quot; **下一步&quot;：查看 + 创建>**。

13. 验证将在应用程序网关上运行;如果成功，请选择 &quot; **创建** " 部分中创建规则。 将每个规则与以前创建的侦听器相关联。 然后配置主后端池和 HTTP 设置。 选择 **添加** 。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-07.png" alt-text="应用程序网关创建&quot;:::

3. 选择前端 IP 地址类型。 对于 &quot;公用&quot;，请选择现有的公共 IP 地址，或创建一个新的 IP 地址。 选择 **下一步：后端>**。

    > [!NOTE]
    > 专用前端仅支持标准和 Web 应用程序防火墙 (WAF) Sku。

4. 接下来，添加一个后端池，该池描述作为应用程序或服务一部分的一组实例，在这种情况下，在 Azure VMware 解决方案基础结构) 上运行的虚拟机 (。 提供 Azure VMware 解决方案私有云上运行的 web 服务器的详细信息，并选择 &quot; **添加**&quot;。然后选择 &quot; **下一步：配置>**。

1. 在 &quot; **配置** &quot; 选项卡上，选择 &quot; **添加路由规则**&quot;。

6. 在 &quot; **侦听器** &quot; 选项卡上，提供侦听器的详细信息。 如果选择了 HTTPS，则必须提供证书，无论是从 PFX 文件还是从 Azure Key Vault 中的现有证书。 

7. 选择 &quot; **后端目标** &quot; 选项卡，然后选择以前创建的后端池。 对于 &quot; **HTTP 设置** &quot; 字段，请选择 &quot; **新增**&quot;。

8. 配置 HTTP 设置的参数。 选择 **添加** 。

9. 如果要配置基于路径的规则，请选择 &quot; **添加多个目标&quot; 以创建基于路径的规则**。 

10. 添加基于路径的规则，然后选择 &quot; **添加**&quot;。 重复此步骤以添加其他基于路径的规则。 

11. 添加完基于路径的规则后，请再次选择 &quot; **添加** &quot;;然后选择 &quot; **下一步：标记&quot;>**。 

12. 添加任何所需的标记。 选择 &quot; **下一步&quot;：查看 + 创建>**。

13. 验证将在应用程序网关上运行;如果成功，请选择 &quot; **创建** " 部分中，复制 "公共 IP 地址"。 然后打开新的浏览器窗口并输入 URL `http://<app-gw-ip-address>:8080` 。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-08.png" alt-text="应用程序网关创建&quot;:::

3. 选择前端 IP 地址类型。 对于 &quot;公用&quot;，请选择现有的公共 IP 地址，或创建一个新的 IP 地址。 选择 **下一步：后端>**。

    > [!NOTE]
    > 专用前端仅支持标准和 Web 应用程序防火墙 (WAF) Sku。

4. 接下来，添加一个后端池，该池描述作为应用程序或服务一部分的一组实例，在这种情况下，在 Azure VMware 解决方案基础结构) 上运行的虚拟机 (。 提供 Azure VMware 解决方案私有云上运行的 web 服务器的详细信息，并选择 &quot; **添加**&quot;。然后选择 &quot; **下一步：配置>**。

1. 在 &quot; **配置** &quot; 选项卡上，选择 &quot; **添加路由规则**&quot;。

6. 在 &quot; **侦听器** &quot; 选项卡上，提供侦听器的详细信息。 如果选择了 HTTPS，则必须提供证书，无论是从 PFX 文件还是从 Azure Key Vault 中的现有证书。 

7. 选择 &quot; **后端目标** &quot; 选项卡，然后选择以前创建的后端池。 对于 &quot; **HTTP 设置** &quot; 字段，请选择 &quot; **新增**&quot;。

8. 配置 HTTP 设置的参数。 选择 **添加** 。

9. 如果要配置基于路径的规则，请选择 &quot; **添加多个目标&quot; 以创建基于路径的规则**。 

10. 添加基于路径的规则，然后选择 &quot; **添加**&quot;。 重复此步骤以添加其他基于路径的规则。 

11. 添加完基于路径的规则后，请再次选择 &quot; **添加** &quot;;然后选择 &quot; **下一步：标记&quot;>**。 

12. 添加任何所需的标记。 选择 &quot; **下一步&quot;：查看 + 创建>**。

13. 验证将在应用程序网关上运行;如果成功，请选择 &quot; **创建** ":::

    将 URL 更改为 `http://<app-gw-ip-address>:8080/images/test.htm`。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-09.png" alt-text="应用程序网关创建&quot;:::

3. 选择前端 IP 地址类型。 对于 &quot;公用&quot;，请选择现有的公共 IP 地址，或创建一个新的 IP 地址。 选择 **下一步：后端>**。

    > [!NOTE]
    > 专用前端仅支持标准和 Web 应用程序防火墙 (WAF) Sku。

4. 接下来，添加一个后端池，该池描述作为应用程序或服务一部分的一组实例，在这种情况下，在 Azure VMware 解决方案基础结构) 上运行的虚拟机 (。 提供 Azure VMware 解决方案私有云上运行的 web 服务器的详细信息，并选择 &quot; **添加**&quot;。然后选择 &quot; **下一步：配置>**。

1. 在 &quot; **配置** &quot; 选项卡上，选择 &quot; **添加路由规则**&quot;。

6. 在 &quot; **侦听器** &quot; 选项卡上，提供侦听器的详细信息。 如果选择了 HTTPS，则必须提供证书，无论是从 PFX 文件还是从 Azure Key Vault 中的现有证书。 

7. 选择 &quot; **后端目标** &quot; 选项卡，然后选择以前创建的后端池。 对于 &quot; **HTTP 设置** &quot; 字段，请选择 &quot; **新增**&quot;。

8. 配置 HTTP 设置的参数。 选择 **添加** 。

9. 如果要配置基于路径的规则，请选择 &quot; **添加多个目标&quot; 以创建基于路径的规则**。 

10. 添加基于路径的规则，然后选择 &quot; **添加**&quot;。 重复此步骤以添加其他基于路径的规则。 

11. 添加完基于路径的规则后，请再次选择 &quot; **添加** &quot;;然后选择 &quot; **下一步：标记&quot;>**。 

12. 添加任何所需的标记。 选择 &quot; **下一步&quot;：查看 + 创建>**。

13. 验证将在应用程序网关上运行;如果成功，请选择 &quot; **创建** ":::

    再次将 URL 更改为 `http://<app-gw-ip-address>:8080/video/test.htm` 。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-10.png" alt-text="应用程序网关创建&quot;:::

3. 选择前端 IP 地址类型。 对于 &quot;公用&quot;，请选择现有的公共 IP 地址，或创建一个新的 IP 地址。 选择 **下一步：后端>**。

    > [!NOTE]
    > 专用前端仅支持标准和 Web 应用程序防火墙 (WAF) Sku。

4. 接下来，添加一个后端池，该池描述作为应用程序或服务一部分的一组实例，在这种情况下，在 Azure VMware 解决方案基础结构) 上运行的虚拟机 (。 提供 Azure VMware 解决方案私有云上运行的 web 服务器的详细信息，并选择 &quot; **添加**&quot;。然后选择 &quot; **下一步：配置>**。

1. 在 &quot; **配置** &quot; 选项卡上，选择 &quot; **添加路由规则**&quot;。

6. 在 &quot; **侦听器** &quot; 选项卡上，提供侦听器的详细信息。 如果选择了 HTTPS，则必须提供证书，无论是从 PFX 文件还是从 Azure Key Vault 中的现有证书。 

7. 选择 &quot; **后端目标** &quot; 选项卡，然后选择以前创建的后端池。 对于 &quot; **HTTP 设置** &quot; 字段，请选择 &quot; **新增**&quot;。

8. 配置 HTTP 设置的参数。 选择 **添加** 。

9. 如果要配置基于路径的规则，请选择 &quot; **添加多个目标&quot; 以创建基于路径的规则**。 

10. 添加基于路径的规则，然后选择 &quot; **添加**&quot;。 重复此步骤以添加其他基于路径的规则。 

11. 添加完基于路径的规则后，请再次选择 &quot; **添加** &quot;;然后选择 &quot; **下一步：标记&quot;>**。 

12. 添加任何所需的标记。 选择 &quot; **下一步&quot;：查看 + 创建>**。

13. 验证将在应用程序网关上运行;如果成功，请选择 &quot; **创建** ":::

## <a name="next-steps"></a>后续步骤

有关更多配置示例，请查看 [Azure 应用程序网关文档](../application-gateway/index.yml) 。