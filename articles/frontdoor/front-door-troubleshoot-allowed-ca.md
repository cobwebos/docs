---
title: 允许 CA 在 Azure 前门上启用自定义 HTTPS
description: 若要使用自己的证书对自定义域启用 HTTPS，必须使用获准的证书颁发机构 (CA) 来创建证书。
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: duau
ms.openlocfilehash: 7bdef37561687b49b030d8237472c0d35f945c13
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399117"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>允许用于在 Azure 前门上启用自定义 HTTPS 的证书颁发机构

对于 Azure 前门自定义域，当你 [使用自己的证书启用 HTTPS 功能](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)时，必须使用允许的证书颁发机构 (CA) 来创建 TLS/SSL 证书。 否则，如果使用未获准的 CA 或自签名证书，请求会遭拒。

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
