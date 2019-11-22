---
title: 如何在 C++/WinRT 中使用设备上的传感器创建和查找定位点 | Microsoft Docs
description: 深入介绍如何在 C++/WinRT 中使用设备上的传感器创建和查找定位点。
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8ea4abe8dee22a50fc50e8d58f99f382a135b70b
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093083"
---
# <a name="how-to-create-and-locate-anchors-using-on-device-sensors-in-cwinrt"></a>如何在 C++/WinRT 中使用设备上的传感器创建和查找定位点

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Azure 空间定位点可以将设备上的定位传感器数据与所创建的定位点相关联。 此数据还可用于快速确定设备附近是否有任何定位点。 有关详细信息，请参阅[粗略重新局部化](../concepts/coarse-reloc.md)。

## <a name="prerequisites"></a>先决条件

若要完成本指南，请确保做好以下准备：

- 具备 C++ 和 <a href="https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt" target="_blank">Windows 运行时 API</a> 的基本知识。
- 通读 [Azure 空间定位点概述](../overview.md)。
- 完成 [5 分钟快速入门](../index.yml)之一。
- 通读[创建并找到定位点操作说明](../create-locate-anchors-overview.md)。

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```cpp
// Create the ASA factory
SpatialAnchorsFactory m_asaFactory { nullptr };
// . . .

// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider;
sensorProvider = m_asaFactory.CreatePlatformLocationProvider();

// Allow GPS
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);

// Allow WiFi scanning
sensors.WifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

// Allow the set of known BLE beacons
sensors.BluetoothEnabled(true);
sensors.KnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```cpp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = m_asaFactory.CreateNearDeviceCriteria();
nearDeviceCriteria.DistanceInMeters(5.0f);
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = m_asaFactory.CreateAnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]