---
title: Widevine 许可证模板概述 | Microsoft Docs
description: 本主题概述了用于配置 Widevine 许可证的 Widevine 许可证模板。
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: eefe82eb022584029b7afb0f2c3524d400c700bd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="widevine-license-template-overview"></a>Widevine 许可证模板概述
可以使用 Azure 媒体服务允许配置和请求 Google Widevine 许可证。 当播放器尝试播放受 Widevine 保护的内容时，将向许可证交付服务发送请求以获取许可证。 如果许可证服务批准了请求，则该服务将颁发许可证。 许可证将被发送到客户端，并用于解密和播放指定的内容。

Widevine 许可证请求将格式化为 JSON 消息。  

>[!NOTE]
> 可以创建不含任何值的空消息，只是“{}”。 然后，使用默认值创建许可证模板。 默认值适用于大多数情况。 基于 Microsoft 的许可证交付方案应始终使用默认值。 如果需要设置“provider”和“content_id”值，提供程序必须与 Widevine 凭据匹配。

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>JSON 消息
| 名称 | 值 | 说明 |
| --- | --- | --- |
| payload |Base64 编码的字符串 |客户端发送的许可证请求。 |
| content_id |Base64 编码的字符串 |用于为每个 content_key_specs.track_type 派生密钥 ID 与内容密钥的标识符。 |
| provider |字符串 |用于查找内容密钥和策略。 如果将 Microsoft 密钥传送用于 Widevine 许可证传送，则忽略此参数。 |
| policy_name |字符串 |以前注册的策略的名称。 可选。 |
| allowed_track_types |枚举 |SD_ONLY 或 SD_HD。 控制许可证中包含哪些内容密钥。 |
| content_key_specs |JSON 结构的数组，请参阅“内容密钥规范”部分。  |更精细地控制要返回哪些内容密钥。 有关详细信息，请参阅“内容密钥规范”部分。 只能指定 allowed_track_types 和 content_key_specs 值中的一个。 |
| use_policy_overrides_exclusively |布尔值 true 或 false |使用 policy_overrides 所指定的策略属性，并忽略以前存储的所有策略。 |
| policy_overrides |JSON 结构，请参阅“策略重写”部分。 |此许可证的策略设置。  如果此资产具有预定义的策略，则会使用这些指定的值。 |
| session_init |JSON 结构，请参阅“会话初始化”部分。 |向许可证传递可选的数据。 |
| parse_only |布尔值 true 或 false |解析许可证请求，但不颁发许可证。 但是，会在响应中返回许可证请求中的值。 |

## <a name="content-key-specs"></a>内容密钥规范
如果有预先存在的策略，则不需要在内容密钥规范中指定任何值。将使用与此内容关联的预先存在的策略来确定输出保护，例如高带宽数字内容保护 (HDCP) 和副本通用管理系统 (CGMS)。 如果预先存在的策略未注册到 Widevine 许可证服务器，则内容提供者可以在许可证请求中注入值。   

无论 use_policy_overrides_exclusively 选项的值是什么，都必须为所有跟踪指定每个 content_key_specs 值。 

| 名称 | 值 | 说明 |
| --- | --- | --- |
| content_key_specs。 track_type |字符串 |跟踪类型名称。 如果许可证请求中指定了 content_key_specs，请确保显式指定所有跟踪类型。 否则会导致无法播放过去 10 秒的内容。 |
| content_key_specs  <br/> security_level |uint32 |定义客户端对播放稳定性的要求。 <br/> - 需要基于软件的白盒加密。 <br/> - 需要软件加密和模糊处理解码器。 <br/> - 密钥材料和加密操作必须在由硬件支持的可信执行环境中执行。 <br/> - 内容加密和解码必须在由硬件支持的可信执行环境中执行。  <br/> - 加密、解码与媒体（压缩和未压缩）的所有处理必须在由硬件支持的可信执行环境中处理。 |
| content_key_specs <br/> required_output_protection.hdc |字符串：HDCP_NONE、HDCP_V1 和 HDCP_V2 中的一个 |指示是否需要 HDCP。 |
| content_key_specs <br/>key |Base64-<br/>编码的字符串 |用于此跟踪的内容密钥。如果指定，则需要 track_type 或 key_id。 内容提供者可以使用此选项注入此跟踪的内容密钥，而不是让 Widevine 许可证服务器生成或查找密钥。 |
| content_key_specs.key_id |Base64 编码的二进制字符串，16 字节 |密钥的唯一标识符。 |

## <a name="policy-overrides"></a>策略重写
| 名称 | 值 | 说明 |
| --- | --- | --- |
| policy_overrides。 can_play |布尔值 true 或 false |指示允许播放内容。 默认值为 false。 |
| policy_overrides。 can_persist |布尔值 true 或 false |指示可以将许可证保存到非易失性存储器供脱机使用。 默认值为 false。 |
| policy_overrides。 can_renew |布尔值 true 或 false |指示允许续订此许可证。 如果为 true，则可以通过检测信号延长许可证期限。 默认值为 false。 |
| policy_overrides。 license_duration_seconds |int64 |指示此特定许可证的时限。 值 0 表示期限没有限制。 默认值为 0。 |
| policy_overrides。 rental_duration_seconds |int64 |指示允许播放的时期。 值 0 表示期限没有限制。 默认值为 0。 |
| policy_overrides。 playback_duration_seconds |int64 |在许可证期限内开始播放后的观看时限。 值 0 表示期限没有限制。 默认值为 0。 |
| policy_overrides。 renewal_server_url |字符串 |将此许可证的所有检测信号（续订）请求定向到指定的 URL。 仅当 can_renew 为 true 时才使用此字段。 |
| policy_overrides。 renewal_delay_seconds |int64 |license_start_time 之后经过几秒才尝试首次续订。 仅当 can_renew 为 true 时才使用此字段。 默认值为 0。 |
| policy_overrides。 renewal_retry_interval_seconds |int64 |指定在发生失败时，每两次发出后续许可证更新请求所要经历的延迟秒数。 仅当 can_renew 为 true 时才使用此字段。 |
| policy_overrides。 renewal_recovery_duration_seconds |int64 |当尝试了续订但由于许可证服务器发生后端问题而未成功时，可以继续播放的时间段。 值 0 表示期限没有限制。 仅当 can_renew 为 true 时才使用此字段。 |
| policy_overrides。 renew_with_usage |布尔值 true 或 false |指示开始使用时发送许可证以进行续订。 仅当 can_renew 为 true 时才使用此字段。 |

## <a name="session-initialization"></a>会话初始化
| 名称 | 值 | 说明 |
| --- | --- | --- |
| provider_session_token |Base64 编码的字符串 |此会话令牌将传回到许可证，并存在于后续的续订中。 会话令牌不能在会话之外持久保存。 |
| provider_client_token |Base64 编码的字符串 |要在许可证响应中返回的客户端令牌。 如果许可证请求包含客户端令牌，则忽略此值。 客户端令牌可以在许可证会话之外持久保存。 |
| override_provider_client_token |布尔值 true 或 false |如果为 false 并且许可证请求包含客户端令牌，请使用来自请求的令牌，即使此结构中已指定客户端令牌。 如果为 true，则始终使用此结构中指定的令牌。 |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>使用 .NET 类型配置 Widevine 许可证
媒体服务提供了可以用于配置 Widevine 许可证的 .NET API。 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>媒体服务 .NET SDK 中定义的类
下列类是这些类型的定义：

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>示例
以下示例展示了如何使用 .NET API 来配置简单的 Widevine 许可证：

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>另请参阅
[使用 PlayReady 和/或 Widevine DRM 动态通用加密](media-services-protect-with-playready-widevine.md)

