---
title: Azure VPN 网关：配置数据包捕获
description: 了解可以在 VPN 网关上使用的数据包捕获功能，以帮助缩小问题的原因。
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: radwiv
ms.openlocfilehash: 486ac23f26a7eee6b31322de79bfb68076a598ec
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441589"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>为 VPN 网关配置数据包捕获

与连接和性能相关的问题通常很复杂。 只需花费大量时间和精力即可缩小问题的原因。 数据包捕获可帮助你将问题的范围缩小到网络的某些部分。 它可帮助您确定问题是出现在网络的客户端、网络的 Azure 端还是两者之间。 缩小问题范围后，调试和采取补救措施会更有效。

有一些常用的数据包捕获工具。 通过这些工具获取相关数据包捕获可能会很繁琐，尤其是在大容量流量方案中。 Azure VPN 网关数据包捕获提供的筛选功能是一个主要的区别。 可以结合使用 VPN 网关数据包捕获和常用的数据包捕获工具。

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN 网关数据包捕获筛选功能

你可以根据需要在网关上或特定连接上运行 VPN 网关数据包捕获。 还可以同时在多个隧道上运行数据包捕获。 可以捕获单向或双向流量、IKE 和 ESP 流量以及内部数据包以及 VPN 网关上的筛选。

在大容量流量中隔离问题时，使用5元组筛选器 (源子网、目标子网、源端口、目标端口、协议) 和 TCP 标志 (SYN、确认、FIN、URG、PSH、RST) 。

下面的 JSON 和 JSON 架构示例提供每个属性的说明。 下面是在运行数据包捕获时要记住的一些限制：
- 在此处显示的架构中，筛选器是一个数组，但目前只能使用一个筛选器。
- 不能同时运行多个网关范围的数据包捕获。
- 不能同时在单个连接上运行多个数据包捕获。 可以同时在不同的连接上运行多个数据包捕获。
- 每个网关最多可以并行运行 5 个数据包捕获。 这些数据包捕获可以是网关范围内数据包捕获和按连接数据包捕获的组合。

### <a name="example-json"></a>示例 JSON
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>JSON 架构
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="set-up-packet-capture-by-using-powershell"></a>使用 PowerShell 设置数据包捕获

以下示例显示启动和停止数据包捕获的 PowerShell 命令。 有关参数选项的详细信息，请参阅 [此 PowerShell 文档](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)。

### <a name="start-packet-capture-for-a-vpn-gateway"></a>启动 VPN 网关的数据包捕获

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

可以使用可选参数 `-FilterData` 来应用筛选器。

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>停止 VPN 网关的数据包捕获

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>启动 VPN 网关连接的数据包捕获

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

可以使用可选参数 `-FilterData` 来应用筛选器。

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>在 VPN 网关连接上停止数据包捕获

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>重要注意事项

- 运行数据包捕获可能会影响性能。 请记住，如果不需要数据包捕获，请将其停止。
- 建议的最短数据包捕获持续时间为 600 秒。 由于路径上多个组件之间的同步问题，较短的数据包捕获可能不提供完整的数据。
- 数据包捕获数据文件以 PCAP 格式生成。 使用 Wireshark 或其他常用应用程序打开 PCAP 文件。
- 基于策略的网关不支持数据包捕获。

## <a name="next-steps"></a>后续步骤

有关 VPN 网关的详细信息，请参阅 [什么是 Vpn 网关？](vpn-gateway-about-vpngateways.md)。
