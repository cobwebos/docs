---
title: 使用 Azure 设备预配服务 SDK 管理设备注册 | Microsoft Docs
description: 如何使用服务 SDK 管理 IoT 中心设备预配服务中的设备注册
author: yzhong94
ms.author: yizhon
ms.date: 04/04/18
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: 9a68d928c70e1e233f6de7df13441a1f688f456a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629841"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>如何使用 Azure 设备预配服务 SDK 管理设备注册
“设备注册”将创建单台设备或一组设备在某一时刻向设备预配服务进行注册的记录。 注册记录包含注册过程中设备所需的初始配置及所需的 IoT 中心。 这篇文章演示如何使用 Azure IoT 预配服务 SDK 以编程方式管理预配服务的设备注册。  SDK 可从 GitHub 上与 Azure IoT SDK 相同的存储库中获取。

## <a name="prerequisites"></a>先决条件
* 从设备预配服务实例获取连接字符串。
* 为所使用的[证明机制](concepts-security.md#attestation-mechanism)获取设备安全项目：
    * [**受信任的平台模块 (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module)：
        * 单个注册：来自物理设备或 TPM 模拟器的注册 ID 和 TPM 认可密钥。
        * 注册组不适用于 TPM 证明。
    * [**X.509**](/azure/iot-dps/concepts-security)：
        * 个人注册：来自物理设备或 SDK [DICE](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) 仿真器的[叶证书](/azure/iot-dps/concepts-security#leaf-certificate)。
        * 注册组：[CA/根证书](/azure/iot-dps/concepts-security#root-certificate)或[中间证书](/azure/iot-dps/concepts-security#intermediate-certificate)，用于在物理设备上生成设备证书。  它还可以通过 SDK DICE 仿真器生成。
* 具体的 API 调用可能会因语言的不同而有所不同。 有关详细信息，请查看 GitHub 上提供的示例：
   * [Java 预配服务客户端示例](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Node.js 预配服务客户端示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET 预配服务客户端示例](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>创建设备注册
可通过两种方法向预配服务注册设备：

* 注册组是共享 X.509 证书的常见证明机制的一组设备的条目，由[根证书](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate)或[中间证书](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)签名。 建议对共享所需初始配置的大量设备，或者全部转到同一租户的设备使用注册组。 请注意，只能注册使用 X.509 证明机制作为“注册组”的设备。 

    可按以下工作流使用 SDK 创建注册组：

    1. 对于注册组，证明机制使用 X.509 根证书。  使用根证书调用服务 SDK API ```X509Attestation.createFromRootCertificate```，以创建注册的证明。  X.509 根证书在 PEM 文件中提供或以字符串形式提供。
    1. 使用 ```attestation``` 创建的唯一 ```enrollmentGroupId``` 创建一个新的 ```EnrollmentGroup``` 变量。  （可选）可设置 ```Device ID```、```IoTHubHostName```、```ProvisioningStatus``` 等参数。
    2. 使用 ```EnrollmentGroup``` 在后端应用程序中调用服务 SDK API ```createOrUpdateEnrollmentGroup``` 以创建注册组。

* 单独注册是用于单个设备注册的条目。 个人注册可使用 X.509 证书或 SAS 令牌（来自物理或虚拟 TPM）作为证明机制。 对于需要唯一初始配置的设备或仅能通过 TPM 或虚拟 TPM 使用 SAS 令牌作为证明机制的设备，建议为其使用个人注册。 单独注册可能会指定所需 IoT 中心设备 ID。

    可按以下工作流使用 SDK 创建单个注册：
    
    1. 选择 ```attestation``` 机制，可以是 TPM 或 X.509。
        1. **TPM**：将来自物理设备或 TPM 模拟器的认可密钥用作输入，可调用服务 SDK API ```TpmAttestation``` 以创建注册的证明。 
        2. **X.509**：使用客户端证书作为输入，可以调用服务 SDK API ```X509Attestation.createFromClientCertificate``` 来为注册创建证明。
    2. 通过使用 ```attestation``` 创建的唯一 ```registrationId``` 作为输入来创建一个新的 ```IndividualEnrollment``` 变量，前者在设备上或从 TPM 模拟器生成。  （可选）可设置 ```Device ID```、```IoTHubHostName```、```ProvisioningStatus``` 等参数。
    3. 使用 ```IndividualEnrollment``` 在后端应用程序中调用服务 SDK API ```createOrUpdateIndividualEnrollment``` 以创建单个注册。

成功创建注册后，设备预配服务会返回注册结果。 [前面提到的](#prerequisites)示例中演示了此工作流。

## <a name="update-an-enrollment-entry"></a>更新注册条目

创建注册条目后，可能需要更新注册。  可能的方案包括更新所需属性、更新证明方法或撤消设备访问权限。  用于单个注册和组注册的 API 不同，但证明机制没有区别。

可按以下工作流更新注册条目：
* **单个注册**：
    1. 首先使用服务 SDK API ```getIndividualEnrollment``` 从预配服务获取最新注册。
    2. 根据需要修改最新注册的参数。 
    3. 使用最新注册，调用服务 SDK API ```createOrUpdateIndividualEnrollment``` 以更新注册条目。
* **组注册**：
    1. 首先使用服务 SDK API ```getEnrollmentGroup``` 从预配服务获取最新注册。
    2. 根据需要修改最新注册的参数。
    3. 使用最新注册，调用服务 SDK API ```createOrUpdateEnrollmentGroup``` 以更新注册条目。

[前面提到的](#prerequisites)示例中演示了此工作流。

## <a name="remove-an-enrollment-entry"></a>删除注册条目

* 可通过使用 ```registrationId``` 调用服务 SDK API ```deleteIndividualEnrollment``` 来删除单个注册。
* 可通过使用 ```enrollmentGroupId``` 调用服务 SDK API ```deleteEnrollmentGroup``` 来删除组注册。

[前面提到的](#prerequisites)示例中演示了此工作流。

## <a name="bulk-operation-on-individual-enrollments"></a>对单个注册进行批量操作

可根据以下工作流来执行批量操作，以创建、更新或删除多个单个注册：

1. 创建一个包含多个 ```IndividualEnrollment``` 的变量。  语言不同，此变量的实现也有所不同。  查看 GitHub 上的批量操作示例以了解详细信息。
2. 使用 ```BulkOperationMode``` 调用服务 SDK API ```runBulkOperation``` 以实现所需操作和用于单个注册的变量。 支持四种模式：创建、更新、updateIfMatchEtag 和删除。

成功执行操作后，设备预配服务会返回批量操作结果。

[前面提到的](#prerequisites)示例中演示了此工作流。
