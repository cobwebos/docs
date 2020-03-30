---
title: 粗重的重新本地化
description: 了解如何使用粗定位重新本地化来查找您附近的锚点。
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844388"
---
# <a name="coarse-relocalization"></a>粗略重新局部化

粗略的重新本地化是一项功能，它为问题提供了初步答案：*我的设备现在在哪里/我应该观察哪些内容？* 响应并不精确，而是以形式出现：*你靠近这些锚点;尝试定位其中一个*锚点。

粗糙的重新本地化的工作原理是，将各种设备上的传感器读数与锚点的创建和查询相关联。 对于室外场景，传感器数据通常是设备的 GPS（全球定位系统）位置。 当 GPS 不可用或不可靠（如室内）时，传感器数据由 WiFi 接入点和蓝牙信标组成。 所有收集的传感器数据都有助于维护 Azure 空间锚点用于快速确定设备大约 100 米范围内的锚点的空间索引。

通过粗略的重新本地化启用的锚点快速查找简化了由世界范围的锚点集合（例如数百万个地理分布）锚点支持的应用程序的开发。 锚点管理的复杂性被隐藏起来，使您能够将更多精力放在令人敬畏的应用程序逻辑上。 所有锚点重装都由 Azure 空间锚点在后台完成。

## <a name="collected-sensor-data"></a>收集的传感器数据

可以发送到锚点服务的传感器数据是以下数据之一：

* GPS 位置：纬度、经度、高度。
* 范围内 WiFi 接入点的信号强度。
* 蓝牙信标的信号强度范围。

通常，您的应用程序需要获取特定于设备的权限才能访问 GPS、WiFi 或 BLE 数据。 此外，上述某些传感器数据在某些平台上的设计不可用。 考虑到这些情况，传感器数据的收集是可选的，默认情况下处于关闭状态。

## <a name="set-up-the-sensor-data-collection"></a>设置传感器数据收集

让我们从创建传感器指纹提供程序开始，让会话了解它：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[迅速](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++温RT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

接下来，您需要决定要使用哪些传感器进行粗略的重新本地化。 此决策特定于您正在开发的应用程序，但下表中的建议应为您提供一个良好的起点：


|             | 室内 | 户外 |
|-------------|---------|----------|
| Gps         | 关闭 | 启用 |
| WiFi        | 启用 | 打开（可选） |
| BLE 信标 | 打开（可选，有注意事项，见下文） | 关闭 |


### <a name="enabling-gps"></a>启用 GPS

假设应用程序已具有访问设备的 GPS 位置的权限，则可以配置 Azure 空间锚点以使用它：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[迅速](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++温RT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

在应用程序中使用 GPS 时，请记住硬件提供的读数通常是：

* 异步和低频（小于 1 Hz）。
* 不可靠/噪音（平均7米标准差）。

通常，设备操作系统和 Azure 空间锚点都会对原始 GPS 信号执行一些筛选和外推，以试图缓解这些问题。 此额外处理需要额外的收敛时间，因此，为了获得最佳效果，您应该尝试：

* 尽早在应用中创建一个传感器指纹提供程序
* 在多个会话之间保持传感器指纹提供程序处于活动状态
* 在多个会话之间共享传感器指纹提供程序

如果计划在锚点会话之外使用传感器指纹提供程序，请确保在请求传感器估计之前启动它。 例如，以下代码将负责实时更新设备在地图上的位置：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[迅速](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++温RT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>启用 WiFi

假设应用程序已具有访问设备的 WiFi 状态的权限，则可以配置 Azure 空间锚点以使用它：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[迅速](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++温RT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

在应用程序中使用 WiFi 时，请记住硬件提供的读数通常是：

* 异步和低频（小于 0.1 Hz）。
* 在操作系统级别可能会受到限制。
* 不可靠/噪音（平均3 dBm标准差）。

Azure 空间锚点将尝试在会话期间构建经过筛选的 WiFi 信号强度映射，以尝试缓解这些问题。 为获得最佳效果，您应该尝试：

* 在放置第一个锚点之前创建会话。
* 尽可能长时间地保持会话处于活动状态（也就是说，在一个会话中创建所有锚点和查询）。

### <a name="enabling-bluetooth-beacons"></a>启用蓝牙信标

假设应用程序已具有访问设备的蓝牙状态的权限，则可以配置 Azure 空间锚点以使用它：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[迅速](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++温RT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

信标通常是多功能设备，可以在这里配置所有设备，包括 UUD 和 MAC 地址。 这种灵活性对于 Azure 空间锚点来说可能存在问题，因为它认为信标由其 UUD 唯一标识。 未能确保这种唯一性很可能会导致空间虫洞。 为获得最佳效果，您应该：

* 为信标分配唯一的 UUD。
* 部署它们 - 通常采用常规模式，如网格。
* 将唯一信标 UUUD 的列表传递给传感器指纹提供商：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[迅速](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++温RT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Azure 空间锚点将仅跟踪已知信标接近 UUD 列表中的蓝牙信标。 但是，被编程为允许上市的 UUID 的恶意信标仍可能对服务质量产生负面影响。 因此，您应该仅在可以控制其部署的已定空间中使用信标。

## <a name="querying-with-sensor-data"></a>使用传感器数据查询

使用关联的传感器数据创建锚点后，可以使用设备报告的传感器读数开始检索锚点。 您不再需要向服务提供您期望找到的已知锚点列表， 而是让服务知道设备的位置，如其板载传感器所报告的那样。 然后，Azure 空间锚点将找出靠近设备的锚点集，并尝试直观地匹配它们。

要让查询使用传感器数据，首先创建"近设备"条件：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[迅速](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++温RT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

参数`DistanceInMeters`控制我们将探索锚点图以检索内容的远。 例如，假设您已填充了一些空间，锚点密度为每米 2。 此外，设备上的摄像机正在观察单个锚点，并且服务已成功定位它。 您最有可能对检索您在附近放置的所有锚点感兴趣，而不是您当前正在观察的单个锚点。 假设您放置的锚点在图形中连接，则服务可以通过跟踪图形中的边缘来检索您附近的所有锚点。 完成的图形遍历量由 控制`DistanceInMeters`。您将获得连接到您所在的锚点的所有锚点，这些锚点比`DistanceInMeters`更近。

请记住，大`MaxResultCount`值可能会对性能产生负面影响。 将其设置为应用程序的合理值。

最后，您需要告诉会话使用基于传感器的查找：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[迅速](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++温RT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>预期结果

消费级 GPS 设备通常相当不精确。 [Zandenbergen 和 Barbeau （2011）][6]的一项研究报告，使用辅助 GPS （A-GPS） 的手机的中位精度约为 7 米 - 相当大的价值不容忽视！ 为了考虑这些测量误差，服务将锚点视为 GPS 空间中的概率分布。 因此，锚现在最有可能（即置信度超过95%）包含其真实、未知的GPS位置的空间区域。

使用 GPS 查询时，也应用了相同的推理。 该设备被表示为围绕其真实、未知的 GPS 位置的另一个空间置信区域。 发现附近的锚点意味着只需找到具有*足够接近*设备置信区域的置信区域的锚点，如下图所示：

![使用 GPS 选择锚点候选](media/coarse-reloc-gps-separation-distance.png)

GPS 信号的精度，无论是在锚点创建还是查询期间，都对返回的锚点集有很大的影响。 相反，基于 WiFi/信标的查询将考虑与查询至少具有一个接入点/信标的所有锚点。 从这个意义上说，基于 WiFi/信标的查询结果主要取决于接入点/信标的物理范围和环境障碍物。

下表估计了每种传感器类型的预期搜索空间：

| 传感器      | 搜索空间半径（约） | 详细信息 |
|-------------|:-------:|---------|
| Gps         | 20 米 - 30 米 | 由其他因素之间的 GPS 不确定性决定。 报告的数字估计，手机与A-GPS的GPS精度中位数，即7米。 |
| WiFi        | 50 米 - 100 米 | 由无线接入点的范围决定。 取决于频率、发射机强度、物理障碍物、干扰等。 |
| BLE 信标 |  70 米 | 由信标的范围决定。 取决于频率、传输强度、物理障碍物、干扰等。 |

## <a name="per-platform-support"></a>每个平台支持

下表汇总了在每个受支持平台上收集的传感器数据，以及任何特定于平台的警告：


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| Gps         | 空值 | 通过[位置管理器][3]API（GPS 和网络）支持 | 通过[CLLocationManager][4] API 支持 |
| WiFi        | 支持的速度约为每 3 秒扫描一次 | 支持。 从 API 级别 28 开始，WiFi 扫描每 2 分钟限制 4 次呼叫。 从 Android 10 中，可以从"开发人员设置"菜单中禁用限制。 有关详细信息，请参阅 Android[文档][5]。 | 不适用 - 无公共 API |
| BLE 信标 | 仅限于[艾迪斯通][1]和[iBeacon][2] | 仅限于[艾迪斯通][1]和[iBeacon][2] | 仅限于[艾迪斯通][1]和[iBeacon][2] |

## <a name="next-steps"></a>后续步骤

在应用中使用粗重的重新本地化。

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [目标C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [迅速](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
