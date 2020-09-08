---
author: baanders
description: Azure 数字孪生的 include 文件 - 列举 Cloud Shell 身份验证的已知问题
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290085"
---
>[!NOTE]
>目前，Cloud Shell 中存在一个已知问题，该问题会影响以下命令组：`az dt route`、`az dt model` 和 `az dt twin`。
>
>若要解决此问题，请在运行命令之前在 Cloud Shell 中运行 `az login`，或者使用[本地 CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 而不使用 Cloud Shell。 有关此操作的详细信息，请参阅[*故障排除：Azure 数字孪生中的已知问题*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)。