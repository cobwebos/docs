---
title: 네트워킹 고려 사항
description: 了解 ASE 网络流量以及如何通过 ASE 设置网络安全组和用户定义的路由。
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 01/24/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fb931c309b5f85902d8abc9cc6da45576bff4041
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713195"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>App Service Environment에 대한 네트워킹 고려 사항 #

## <a name="overview"></a>개요 ##

 Azure[应用服务环境][Intro]是将 Azure App Service 部署到 azure 虚拟网络（VNet）中的子网。 ASE(App Service Environment)에는 두 가지 배포 유형이 있습니다.

- **외부 ASE** - ASE에서 호스트되는 앱을 인터넷 액세스가 가능한 IP 주소에 표시합니다. 有关详细信息，请参阅[创建外部 ASE][MakeExternalASE]。
- **ILB ASE** - ASE에서 호스트되는 앱을 VNet 내부의 IP 주소에 표시합니다. 내부 엔드포인트는 ILB(내부 부하 분산 장치)이므로 ILB ASE라고 합니다. 有关详细信息，请参阅[创建和使用 ILB ASE][MakeILBASE]。

所有 Ase、External 和 ILB 都有一个公共 VIP，用于入站管理流量，并在从 ASE 调用到 internet 时作为发件人地址。 从 ASE 发送到 internet 的调用会将 VNet 保留给为 ASE 分配的 VIP。 이 VIP의 공용 IP는 인터넷으로 이동하는 ASE의 모든 호출에 대한 원본 IP입니다. 사용자의 ASE에 있는 앱이 VNet 또는 VPN의 리소스를 호출하는 경우 원본 IP는 ASE에서 사용되는 서브넷의 IP 중 하나가 됩니다. ASE가 VNet 내에 있으므로 추가 구성 없이 VNet 내 리소스에 액세스할 수 있습니다. VNet이 온-프레미스 네트워크에 연결되어 있으면 ASE의 앱도 추가 구성 없이 해당 네트워크의 리소스에 액세스할 수 있습니다.

![외부 ASE][1] 

외부 ASE가 있는 경우에는 공용 VIP가 다음 항목용으로 ASE 앱을 확인하는 엔드포인트로도 사용됩니다.

* HTTP/S 
* FTP/S
* Web 部署
* 원격 디버깅

![ILB ASE][2]

如果使用 ILB ASE，则 ILB 地址的地址是用于 HTTP/S、FTP/S、web 部署和远程调试的终结点。

## <a name="ase-subnet-size"></a>ASE 서브넷 크기 ##

ASE를 배포한 후에는 ASE를 호스팅하는 데 사용되는 서브넷의 크기를 변경할 수 없습니다.  ASE는 격리된 각 App Service 계획 인스턴스뿐만 아니라 각 인프라 역할에 대한 주소를 사용합니다.  此外，对于创建的每个子网，Azure 网络都使用5个地址。  App Service 계획이 아예 없는 ASE는 앱을 만들기 전에 12개의 주소를 사용합니다.  如果它是 ILB ASE，则在该 ASE 中创建应用前，它将使用13个地址。 ASE를 규모 확장할 때 인프라 역할은 App Service 계획 인스턴스의 15 및20개의 배수마다 추가됩니다.

   > [!NOTE]
   > ASE 이외의 항목을 서브넷에 포함해서는 안 됩니다. 향후 확장이 가능한 주소 공간을 선택해야 합니다. 이 설정은 나중에 변경할 수 없습니다. 주소 256개를 포함할 수 있는 `/24` 크기를 사용하는 것이 좋습니다.

규모를 확장 또는 축소할 때 적절한 크기의 새 역할이 추가된 다음, 워크로드가 현재 크기에서 대상 크기로 마이그레이션됩니다. 仅在迁移工作负荷后删除原始 Vm。 如果你的 ASE 包含 100 ASP 实例，则可能需要两倍于 Vm 的数量。  이러한 이유로 필요할 수 있는 모든 변경 사항을 수용하기 위해 '/24'를 사용하는 것이 좋습니다.  

## <a name="ase-dependencies"></a>ASE 종속성 ##

### <a name="ase-inbound-dependencies"></a>ASE 인바운드 종속성 ###

仅为 ASE 操作，ASE 需要打开以下端口：

| 사용 | 최저 | - |
|-----|------|----|
| 관리 | App Service 관리 주소 | ASE 서브넷: 454, 455 |
|  ASE 내부 통신 | ASE 서브넷: 모든 포트 | ASE 서브넷: 모든 포트
|  Azure Load Balancer 인바운드 허용 | Azure Load Balancer | ASE 子网：16001

在端口扫描上，有2个其他端口可显示为打开状态，7654和1221。 它们使用 IP 地址进行回复，而不是更多。 如果需要，可以将其阻止。 

인바운드 관리 트래픽은 시스템 모니터링 외에도 ASE의 명령 및 제어를 제공합니다. 此流量的源地址列在[ASE 管理地址][ASEManagement]文档中。 网络安全配置需要允许从端口454和455上的 ASE 管理地址进行访问。 해당 주소의 액세스를 차단하면 ASE가 비정상화된 다음, 일시 중단됩니다. 포트 454 및 455에서 들어오는 TCP 트래픽은 동일한 VIP에서 되돌아 가야 합니다. 그렇지 않으면 비대칭 라우팅 문제가 발생합니다. 

在 ASE 子网中，有许多端口用于内部组件通信，它们可能会更改。 이를 위해서는 ASE 서브넷에 있는 모든 포트를 ASE 서브넷에서 액세스할 수 있어야 합니다. 

Azure Load Balancer 및 ASE 서브넷 간의 통신을 위해서는 최소 포트 454, 455 및 16001이 열려 있어야 합니다. 16001 포트는 부하 분산 장치 및 ASE 사이에 활성 트래픽을 유지하는 데 사용됩니다. 如果使用 ILB ASE，则可以将流量锁定为仅限454、455、16001端口。  如果使用的是外部 ASE，则需要考虑普通应用访问端口。  

你需要考虑的其他端口是应用程序端口：

| 사용 | 포트 |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio 원격 디버깅  |  4020, 4022, 4024 |
|  Web 部署服务 | 8172 |

如果你阻止应用程序端口，ASE 仍可正常工作，但你的应用程序可能无法正常工作。  如果要将应用分配的 IP 地址与外部 ASE 一起使用，则需要允许将流量从分配给应用的 ip 地址发送到 ASE 门户中显示的端口上的 ASE 子网 > IP 地址 "页。

### <a name="ase-outbound-dependencies"></a>ASE 아웃바운드 종속성 ###

아웃 바운드 액세스의 경우 ASE는 여러 외부 시스템에 종속됩니다. 그러한 시스템 종속성은 대부분 DNS 이름으로 정의되며 고정된 IP 주소 집합에 매핑되지 않습니다. 따라서 ASE는 여러 포트를 통해 ASE 서브넷에서 모든 외부 IP에 아웃바운드로 액세스할 수 있어야 합니다. 

ASE 在以下端口上与可访问 internet 的地址通信：

| 용도 | 포트 |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| CRL，Windows 更新，Linux 依赖项，Azure 服务 | 80/443 |
| Azure SQL | 1433 | 
| 모니터링 | 12000 |

出站依赖项在描述[锁定应用服务环境出站流量](./firewall-integration.md)的文档中列出。 ASE가 해당 종속성에 액세스하지 못하는 경우 작동이 중지됩니다. 이러한 작동 중지 상태가 오랜 기간 지속되면 ASE가 일시 중단됩니다. 

### <a name="customer-dns"></a>고객 DNS ###

VNet이 고객 정의 DNS 서버로 구성된 경우 테넌트 워크로드가 해당 서버를 사용합니다. ASE 出于管理目的使用 Azure DNS。 如果使用客户选择的 DNS 服务器配置了 VNet，则必须可从包含 ASE 的子网访问 DNS 服务器。

若要从 web 应用测试 DNS 解析，可以使用控制台命令*nameresolver*。 앱에 대한 scm 사이트에서 디버그 창으로 이동하거나 포털의 앱으로 이동하고 콘솔을 선택합니다. 在 shell 提示符下，可以发出命令*nameresolver*以及要查找的 DNS 名称。 얻게 되는 결과는 동일한 조회를 수행하는 동안 앱이 얻는 결과와 동일합니다. 如果使用 nslookup，将改为使用 Azure DNS 进行查找。

ASE가 있는 VNet의 DNS 설정을 변경하면 ASE를 재부팅해야 합니다. ASE를 재부팅하지 않으려면 ASE를 만들기 전에 VNet에 대한 DNS 설정을 구성하는 것이 좋습니다.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>포털 종속성 ##

ASE의 기능적 종속성 외에 포털 환경과 관련된 몇 가지 추가 항목이 있습니다. Azure Portal의 기능 중 일부는 _SCM 사이트_에 대한 직접 액세스에 의존합니다. Azure App Service의 모든 앱에는 URL이 두 개 있습니다. 첫 번째 URL은 앱에 액세스하는 것입니다. 두 번째 URL은 _Kudu 콘솔_이라고도 하는 SCM 사이트에 액세스하는 것입니다. SCM 사이트를 사용하는 기능은 다음과 같습니다.

-   웹 작업
-   Functions
-   스트리밍 로그
-   Kudu
-   확장
-   프로세스 탐색기
-   Console

使用 ILB ASE 时，无法从 VNet 外部访问 SCM 站点。 某些功能将不能从应用门户工作，因为它们需要访问应用的 SCM 站点。 你可以直接连接到 SCM 站点，而不是使用门户。 

如果 ILB ASE 是域名*contoso.appserviceenvironment.net* ，而你的应用名称为*testapp*，则会在*testapp.contoso.appserviceenvironment.net*上访问应用。 在*testapp.scm.contoso.appserviceenvironment.net*上访问了与之配合的 SCM 站点。

## <a name="ase-ip-addresses"></a>ASE IP 주소 ##

ASE에는 알고 있어야 할 몇 가지 IP 주소가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- **공용 인바운드 IP 주소**: 외부 ASE의 앱 트래픽 및 외부 ASE와 ILB ASE 둘 다의 관리 트래픽에 사용됩니다.
- **아웃바운드 공용 IP**: VNet에서 시작되는 ASE로부터의 아웃바운드 연결(VPN으로 라우팅되지 않음)의 "시작" IP로 사용됩니다.
- **ILB ip 地址**： ILB ip 地址仅在 ILB ASE 中存在。
- **앱에 할당된 IP 기반 SSL 주소**: 외부 ASE를 사용하며 IP 기반 SSL이 구성되어 있을 때만 사용 가능합니다.

所有这些 IP 地址都在 ASE UI 的 Azure 门户中可见。 ILB ASE를 사용하는 경우 ILB용 IP가 나열됩니다.

   > [!NOTE]
   > 이러한 IP 주소는 ASE가 켜져 있고 실행 중이기만 하다면 변경되지 않습니다.  ASE가 일시 중단되고 복원되면 ASE에서 사용하는 주소가 변경됩니다. ASE가 일시 중단되는 일반적인 원인은 인바운드 관리 액세스를 차단하거나 ASE 종속성에 대한 액세스를 차단하는 경우입니다. 

![IP 주소][3]

### <a name="app-assigned-ip-addresses"></a>앱에 할당된 IP 주소 ###

외부 ASE를 사용하면 IP 주소를 개별 앱에 할당할 수 있습니다. ILB ASE에서는 IP 주소를 할당할 수 없습니다. 若要详细了解如何将应用配置为具有其自己的 IP 地址，请参阅[在 Azure App Service 中使用 SSL 绑定保护自定义 DNS 名称](../configure-ssl-bindings.md)。

앱에 고유한 IP 기반 SSL 주소가 있는 경우 ASE는 해당 IP 주소에 매핑하도록 두 개의 포트를 예약합니다. 한 포트는 HTTP 트래픽용이고, 다른 포트는 HTTPS용입니다. 이러한 포트는 IP 주소 섹션의 ASE UI에 나열됩니다. 트래픽은 VIP에서 해당 포트에 연결할 수 있어야 합니다. 그렇지 않으면 앱에 액세스할 수 없습니다. NSG(네트워크 보안 그룹)를 구성할 때는 이 요구 사항을 고려해야 합니다.

## <a name="network-security-groups"></a>네트워크 보안 그룹 ##

[网络安全组][NSGs]提供了控制 VNet 中的网络访问的功能。 포털을 사용할 때는 모든 항목을 거부하는 최저 우선 순위의 암시적 거부 규칙이 있습니다. 허용 규칙은 사용자가 직접 작성합니다.

ASE에서 사용자는 ASE 자체를 호스팅하는 데 사용된 VM에 대한 액세스 권한이 없습니다. Microsoft에서 관리하는 구독을 통해 이러한 VM에 액세스할 수 있습니다. ASE에서 앱에 대한 액세스를 제한하려는 경우 ASE 서브넷에 대해 NSG를 설정합니다. 이 과정에서 ASE 종속성에 유의하세요. 종속성을 차단하면 ASE 작동이 중지됩니다.

NSG는 Azure Portal 또는 PowerShell을 통해 구성할 수 있습니다. 이 문서에서는 Azure Portal의 정보를 제공합니다. **네트워킹** 아래에서 최상위 리소스로 포털에서 NSG를 만들고 관리합니다.

要使 ASE 正常工作，NSG 中的必需条目为允许流量：

**站**
* 在端口454455上从 IP 服务标记 AppServiceManagement
* 从端口16001上的负载均衡器
* 从 ASE 子网到所有端口上的 ASE 子网

**Outbound**
* 端口123上的所有 Ip
* 端口80、443上的所有 Ip
* 端口1433上的 IP 服务标记 AzureSQL
* 端口12000上的所有 Ip
* 到所有端口上的 ASE 子网

不需要在 dns 中添加 DNS 端口，因为 NSG 规则不会影响 dns 的流量。 这些端口不包括应用成功使用所需的端口。 기본 앱 액세스 포트는 다음과 같습니다.

| 사용 | 포트 |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio 원격 디버깅  |  4020, 4022, 4024 |
|  Web 部署服务 | 8172 |

인바운드 및 아웃바운드 요구 사항을 고려할 때, NSG는 이 예제에 나와 있는 NSG와 비슷하게 표시되어야 합니다. 

![인바운드 보안 규칙][4]

기본 규칙을 사용하면 VNet의 IP가 ASE 서브넷과 통신할 수 있습니다. 공용 VIP라고도 하는 부하 분산 장치가 ASE와 통신할 수 있도록 하는 또 다른 기본 규칙도 있습니다. 기본 규칙을 확인하려면 **추가** 아이콘 옆에 있는 **기본 규칙**을 선택합니다. 如果将拒绝所有其他规则置于默认规则之前，则会阻止 VIP 和 ASE 之间的流量。 VNet 내에서 들어오는 트래픽을 차단하려면 인바운드를 허용하는 규칙을 직접 추가합니다. 이때 대상이 **Any**이고 포트 범위가 **\*** 인 AzureLoadBalancer와 같은 원본을 사용합니다. NSG 규칙은 ASE 서브넷에 적용되므로 대상을 구체적으로 지정할 필요는 없습니다.

앱에 IP 주소를 할당한 경우 포트를 열어 두어야 합니다. 포트를 확인하려면 **App Service Environment** > **IP 주소**를 선택합니다.  

다음 아웃바운드 규칙에 표시된 모든 항목이 필요합니다(마지막 항목은 제외). 이러한 항목을 통해 네트워크에서 이 문서 앞부분에서 언급했던 ASE 종속성에 액세스할 수 있습니다. 이러한 항목 중 하나라도 차단하면 ASE의 작동이 중지됩니다. 목록의 마지막 항목은 사용자의 ASE가 VNet의 다른 리소스와 통신할 수 있도록 설정합니다.

![아웃바운드 보안 규칙][5]

NSG를 정의한 후 ASE가 있는 서브넷에 할당합니다. ASE VNet 또는 서브넷을 기억하지 못하는 경우 ASE 포털 페이지에서 확인할 수 있습니다. 서브넷에 NSG를 할당하려면 서브넷 UI로 이동한 다음 NSG를 선택합니다.

## <a name="routes"></a>경로 ##

강제 터널링은 아웃바운드 트래픽이 인터넷으로 직접 가지는 않지만 ExpressRoute 게이트웨이 또는 가상 어플라이언스와 같은 다른 곳으로 이동하도록 VNet에서 경로를 설정하는 경우입니다.  如果需要以这种方式配置 ASE，请阅读有关[使用强制隧道配置应用服务环境][forcedtunnel]的文档。  이 문서에서는 ExpressRoute 및 강제 터널링 작업에 사용할 수 있는 옵션을 알려줍니다.

포털에서 ASE를 만들 때 ASE를 사용하여 만든 서브넷에 경로 테이블의 집합도 만듭니다.  이러한 경로는 단순히 아웃바운드 트래픽을 인터넷에 직접 보내도록 합니다.  
동일한 경로를 수동으로 만들려면 다음 단계를 수행합니다.

1. Azure Portal로 이동합니다. **네트워킹** > **경로 테이블**을 선택합니다.

2. VNet과 동일한 지역에 새 경로 테이블을 만듭니다.

3. 경로 테이블 UI 내에서 **경로** > **추가**를 선택합니다.

4. **다음 홉 형식**을 **인터넷**으로, **주소 접두사**를 **0.0.0.0/0**으로 설정합니다. **저장**을 선택합니다.

    다음과 유사한 출력이 표시됩니다.

    ![기능 경로][6]

5. 새 경로 테이블을 만든 후 ASE가 포함된 서브넷으로 이동합니다. Portal의 목록에서 경로 테이블을 선택합니다. 변경 내용을 저장하면 서브넷이 표시된 경로와 NSG를 확인할 수 있습니다.

    ![NSG 및 경로][7]

## <a name="service-endpoints"></a>서비스 엔드포인트 ##

서비스 엔드포인트를 사용하면 Azure 가상 네트워크 및 서브넷의 집합에 다중 테넌트 서비스에 대한 액세스를 제한할 수 있습니다. 서비스 엔드포인트에 대한 자세한 내용은 [Virtual Network 서비스 엔드포인트][serviceendpoints] 설명서에서 확인할 수 있습니다. 

리소스에서 서비스 엔드포인트를 사용하는 경우 다른 모든 경로에 우선해 만든 경로가 있습니다. 如果在任何 Azure 服务上使用强制隧道 ASE 上的服务终结点，则不会对这些服务的流量进行强制隧道。 

Azure SQL 인스턴스를 통해 서브넷에서 서비스 엔드포인트가 사용되는 경우 해당 서브넷에서 연결된 모든 Azure SQL 인스턴스는 서비스 엔드포인트를 사용할 수 있어야 합니다. 동일한 서브넷에서 여러 Azure SQL 인스턴스에 액세스하려는 경우 다른 Azure SQL 인스턴스가 아닌 한 Azure SQL 인스턴스에서 서비스 엔드포인트를 사용할 수 없습니다. 对于服务终结点，任何其他 Azure 服务的行为类似于 Azure SQL。 Azure Storage를 통해 서비스 엔드포인트를 사용하는 경우 사용자의 서브넷에서 해당 리소스에 대한 액세스를 잠글 수 있지만 서비스 엔드포인트를 사용할 수 없는 경우에도 여전히 다른 Azure Storage 계정에 액세스할 수 있습니다.  

![서비스 엔드포인트][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
