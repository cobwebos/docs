---
title: 有关 Azure 中的 FHIR 服务的常见问题解答 - Azure API for FHIR
description: 获取有关 Azure API for FHIR 的常见问题（例如 FHIR API 后面的数据的存储位置以及版本支持）的解答。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870977"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>有关 Azure API for FHIR 的常见问题解答

## <a name="what-is-fhir"></a>什么是 FHIR？
快速医疗保健互操作性资源（FHIR - 念作“fire”）是一个互操作性标准，旨在实现不同健康系统之间的医疗保健数据交换。 此标准由 HL7 组织制定，由世界各地的医疗保健组织使用。 可用的最新 FHIR 版本为 R4（发行版 4）。 Azure API for FHIR 支持 R4，并支持旧版 STU3（试用版 3 的标准）。 有关 FHIR 的详细信息，请访问 [HL7.org](http://hl7.org/fhir/summary.html)。

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR API 后面的数据是否存储在 Azure 中？

是的，这些数据存储在 Azure 中的托管数据库内。 Azure API for FHIR 不提供对底层数据存储的直接访问。

## <a name="what-identity-provider-do-you-support"></a>你们支持哪些标识提供者？

我们目前支持使用 Microsoft Azure Active Directory 作为标识提供者。

## <a name="what-fhir-version-do-you-support"></a>你们支持哪个 FHIR 版本？

我们支持 Azure API for FHIR (PaaS) 和适用于 Azure 的 FHIR 服务器（开源）上的版本 4.0.0 和 3.0.1。

有关详细信息，请参阅[支持的功能](fhir-features-supported.md)。 在 [HL7 FHIR 的版本历史记录](https://hl7.org/fhir/R4/history.html)中了解不同版本的变化。

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>开源的适用于 Azure 的 Microsoft FHIR 服务器与 Azure API for FHIR 之间有何区别？

Azure API for FHIR 是开源的适用于 Azure 的 Microsoft FHIR 服务器的托管版本。 在托管服务中，Microsoft 将提供所有维护和更新。 

运行适用于 Azure 的 FHIR 服务器时，可以直接访问底层服务。 但是，如果你要存储 PHI 数据，则还要负责维护和更新该服务器，并完成所有必要的合规性工作。

从开发立场讲，每项功能首先会部署到开源的适用于 Azure 的 Microsoft FHIR 服务器。 在开源解决方案中经过验证后，该功能将发布到 PaaS Azure API for FHIR 解决方案。 在开源与 PaaS 解决方案中发布功能的时间差取决于该功能的复杂性以及路线图中的其他优先级设置。 

## <a name="what-is-smart-on-fhir"></a>什么是 SMART on FHIR？

SMART（可替代的医疗应用程序和可重用的技术）on FHIR 是一套开放规范，旨在将合作伙伴应用程序与 FHIR 服务器和其他健康 IT 系统（例如电子健康记录和健康信息交换）相集成。 创建 SMART on FHIR 应用程序能够确保你的应用程序可供大量不同的系统访问和利用。
身份验证和 Azure API for FHIR。 若要详细了解 SMART，请访问 [SMART Health IT](https://smarthealthit.org/)。

## <a name="next-steps"></a>后续步骤

在本文中，你已阅读有关 Azure API for FHIR 的一些常见问题的解答。 接下来，请了解适用于 Azure 的 FHIR 服务器中支持的功能：
 
>[!div class="nextstepaction"]
>[支持的 FHIR 功能](fhir-features-supported.md)