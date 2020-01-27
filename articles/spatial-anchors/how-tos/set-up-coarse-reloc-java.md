---
title: Java 中的粗略重新局部化
description: 深入解释了如何使用 Java 中的粗略重新局部化来创建和查找定位点。
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7a13b3e838bd3083741eb903f39f3f7683f39ee4
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545192"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-java"></a>如何使用 Java 中的粗略重新局部化来创建和查找定位点

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Azure 空间定位点可以将设备上的定位传感器数据与所创建的定位点相关联。 此数据还可用于快速确定设备附近是否有任何定位点。 有关详细信息，请参阅[粗略重新局部化](../concepts/coarse-reloc.md)。

## <a name="prerequisites"></a>必备条件

若要完成本指南，请确保做好以下准备：

- 对 Java 有一个基本的了解。
- 通读 [Azure 空间定位点概述](../overview.md)。
- 完成 [5 分钟快速入门](../index.yml)之一。
- 通读[创建并找到定位点操作说明](../create-locate-anchors-overview.md)。

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Allow GPS
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);

// Allow WiFi scanning
sensors.setWifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

// Allow the set of known BLE beacons
sensors.setBluetoothEnabled(true);
sensors.setKnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```java
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();
nearDeviceCriteria.setDistanceInMeters(5.0f);
nearDeviceCriteria.setMaxResultCount(25);

// Set the session's locate criteria
AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]