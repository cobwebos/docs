# [概述](media-services-overview.md)
## [方案和可用性](scenarios-and-availability.md)
## [概念](media-services-concepts.md)

# 入门
## [创建和管理帐户](media-services-portal-create-account.md)
## [设置开发环境](media-services-set-up-computer.md)
### [.NET](media-services-dotnet-how-to-use.md)
### [REST](media-services-rest-how-to-use.md)  
## [使用 AAD 身份验证访问 API](media-services-use-aad-auth-to-access-ams-api.md)
### [使用门户管理 AAD 身份验证](media-services-portal-get-started-with-aad.md)
### [使用 .NET 访问 API](media-services-dotnet-get-started-with-aad.md)
### [使用 REST 访问 API](media-services-rest-connect-with-aad.md)
### [使用 Azure CLI 创建和配置 AAD 应用](media-services-cli-create-and-configure-aad-app.md)
### [使用 Azure PowerShell 创建和配置 AAD 应用](media-services-powershell-create-and-configure-aad-app.md)

## 传送点播视频
### [Azure portal](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## 执行实时传送视频流
### [Azure portal](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# 如何
## 管理
### 实体
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [流式处理终结点](media-services-streaming-endpoints-overview.md)
#### [Azure portal](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### 存储
#### [轮转存储访问密钥后更新媒体服务](media-services-roll-storage-access-keys.md)
#### [跨多个存储帐户管理资产](meda-services-managing-multiple-storage-accounts.md)
### [配额和限制](media-services-quotas-and-limitations.md)
## [配置 Postman](media-rest-apis-with-postman.md)
### [按需流式处理集合](postman-collection.md)
### [实时流式处理集合](postman-live-streaming-collection.md)
### [环境](postman-environment.md)
## 上传内容
### 将文件上传到帐户中
#### [Azure portal](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [使用 Aspera 上传大型文件](media-services-upload-files-with-aspera.md)
### [使用 StorSimple 上传文件](media-services-upload-files-from-storsimple.md)
### [复制现有的 Blob](media-services-copying-existing-blob.md)

## [对内容进行编码](media-services-encode-asset.md)
### [比较编码器](media-services-compare-encoders.md)
### [管理编码的速度和并发度](media-services-manage-encoding-speed.md)
### Media Encoder Standard (MES)
#### [Media Encoder Standard 格式和编解码器](media-services-media-encoder-standard-formats.md)
#### [使用 MES 自动生成比特率阶梯](media-services-autogen-bitrate-ladder-with-mes.md)
#### 使用 Media Encoder Standard 进行编码
##### [Azure portal](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST](media-services-rest-encode-asset.md)
#### [使用 MES 进行高级编码](media-services-advanced-encoding-with-mes.md)
##### [自定义 Media Encoder Standard 预设](media-services-custom-mes-presets-with-dotnet.md)
##### [如何使用 Media Encoder Standard 通过 .NET 来生成缩略图](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [使用 Media Encoder Standard 剪辑视频](media-services-crop-video.md)
#### MES 架构
##### [Media Encoder Standard 架构](media-services-mes-schema.md)
##### [输入元数据](media-services-input-metadata-schema.md)
##### [输出元数据](media-services-output-metadata-schema.md)
#### [MES 预设](media-services-mes-presets-overview.md) 
##### [H264 多比特率 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [H264 多比特率 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [H264 多比特率 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 多比特率 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [H264 多比特率 16x9 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [H264 多比特率 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [H264 多比特率 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [H264 多比特率 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 多比特率 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [H264 多比特率 4x3 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [H264 多比特率 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [H264 多比特率 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [H264 单比特率 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [H264 单比特率 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [H264 单比特率 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 单比特率 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [H264 单比特率 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [H264 单比特率 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [H264 单比特率 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 单比特率 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [H264 单比特率 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [H264 单比特率 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [H264 单比特率 720p (Android)](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [H264 单比特率高品质 SD (Android)](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [H264 单比特率低品质 SD (Android)](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### 媒体编码器高级工作流
#### [媒体编码器高级工作流格式和编解码器](media-services-premium-workflow-encoder-formats.md)
#### 使用媒体编码器高级工作流进行编码
##### [媒体编码器高级工作流](media-services-encode-with-premium-workflow.md)
##### [媒体编码器高级工作流教程](media-services-media-encoder-premium-workflow-tutorials.md)
##### [使用工作流设计器创建高级编码工作流](media-services-workflow-designer.md)
##### [使用多个输入的高级工作流](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [创建生成 fMP4 区块的任务](media-services-generate-fmp4-chunks.md)
### 媒体处理器
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)
### [错误代码](media-services-encoding-error-codes.md)
### 已放弃
#### [静态打包和加密](media-services-static-packaging.md)

## [实时流](media-services-manage-channels-overview.md)
### [本地编码器](media-services-live-streaming-with-onprem-encoders.md)
#### [推荐的本地编码器](media-services-recommended-encoders.md)
#### [Azure portal](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
### [使用云编码器实时传送视频流](media-services-manage-live-encoder-enabled-channels.md)
#### [Azure portal](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [配置与云编码器一起使用的本地编码器](media-services-live-encoders-overview.md)
#### [Elemental Live 编码器](media-services-configure-elemental-live-encoder.md)
#### [FMLE 编码器](media-services-configure-fmle-live-encoder.md)
#### [Haivision KB 编码器](media-services-configure-kb-live-encoder.md)
#### [NewTek TriCaster 编码器](media-services-configure-tricaster-live-encoder.md)
#### [Wirecast 编码器](media-services-configure-wirecast-live-encoder.md)
### [处理长时间运行的操作](media-services-dotnet-long-operations.md)
### [碎片化的 MP4 实时引入规范](media-services-fmp4-live-ingest-overview.md)

## [剪辑内容](media-services-azure-media-clipper-overview.md)
### [入门](media-services-azure-media-clipper-getting-started.md)
### [加载视频](media-services-azure-media-clipper-load-assets.md)
### [配置键盘快捷方式](media-services-azure-media-clipper-keyboard-shortcuts.md)
### [配置本地化](media-services-azure-media-clipper-localization.md)
### [提交剪辑作业](media-services-azure-media-clipper-submit-job.md)
### [Azure portal](media-services-azure-media-clipper-portal.md)

## [保护内容](media-services-content-protection-overview.md)
### [存储加密](media-services-rest-storage-encryption.md)
### [AES-128 加密](media-services-protect-with-aes128.md)
### [适用于流式处理的 PlayReady/Widevine](media-services-protect-with-playready-widevine.md)
### [适用于流式处理的 FairPlay](media-services-protect-hls-with-fairplay.md)
### [适用于 Windows 10 的脱机 PlayReady](https://blogs.msdn.microsoft.com/playready4/2016/10/26/does-azure-media-services-support-offline-mode/)
### [适用于 iOS 的脱机 FairPlay](media-services-protect-hls-with-offline-fairplay.md)
### [适用于 Android 的脱机 Widevine](offline-widevine-for-android.md)
### [在 Azure 门户中配置](media-services-portal-protect-content.md)
### [传送 DRM 许可证](media-services-deliver-keys-and-licenses.md)
### 创建内容密钥
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### 许可证模板概述
#### [PlayReady 许可证模板](media-services-playready-license-template-overview.md)
#### [Widevine 许可证模板](media-services-widevine-license-template-overview.md)
### 配置资产传送策略
#### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
#### [REST](media-services-rest-configure-asset-delivery-policy.md)
### 配置内容密钥授权策略
#### [Azure portal](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)
### [将身份验证令牌传递到 AMS](media-services-pass-authentication-tokens.md)
### 引用设计
#### [混合 DRM 系统设计](hybrid-design-drm-sybsystem.md)
#### [引用多 DRM 设计](media-services-cenc-with-multidrm-access-control.md)

## [分析](media-services-analytics-overview.md)
### [使用 Azure 门户分析媒体](media-services-portal-analyze.md)
### [使用 Indexer 2 进行处理](media-services-process-content-with-indexer2.md)
### [使用 Indexer 进行处理](media-services-index-content.md)
#### [任务预设](indexer-task-preset.md)
### [使用 Hyperlapse 进行处理](media-services-hyperlapse-content.md)
### [使用 Face Detector 进行处理](media-services-face-and-emotion-detection.md)
### [使用 Motion Detector 进行处理](media-services-motion-detection.md)
### [使用 Face Redactor 进行处理](media-services-face-redaction.md)
#### [Face Redactor 演练](media-services-redactor-walkthrough.md)
### [使用视频缩略图进行处理](media-services-video-summarization.md)
### [使用 OCR 进行处理](media-services-video-optical-character-recognition.md)
### [使用内容审查器进行处理](media-services-content-moderation.md)

## [配置遥测](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST](media-services-rest-telemetry.md)

## 缩放
### [媒体处理](media-services-scale-media-processing-overview.md)
#### [Azure portal](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
### 流式处理终结点
#### [Azure portal](media-services-portal-scale-streaming-endpoints.md)

## [传送内容](media-services-deliver-content-overview.md)
### [动态打包](media-services-dynamic-packaging-overview.md)
### [筛选器和动态清单概述](media-services-dynamic-manifest-overview.md)
#### [使用 .NET 创建筛选器](media-services-dotnet-dynamic-manifest.md)
#### [使用 REST 创建筛选器](media-services-rest-dynamic-manifest.md)
### [媒体服务扩展中的 CDN 缓存策略](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### 发布内容
#### [Azure portal](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [通过下载传送](media-services-deliver-asset-download.md)
### [故障转移流式处理方案](media-services-implement-failover.md)

## 使用
### [使用现有播放器播放媒体](media-services-playback-content-with-existing-players.md)
### [使用媒体播放器播放媒体](media-services-develop-video-players.md)
### 其他播放选项
#### [平滑流式处理 Windows 应用商店应用程序](media-services-build-smooth-streaming-apps.md)
#### [使用 DASH.js 的 HTML5 应用程序](media-services-embed-mpeg-dash-in-html5.md)
#### [Adobe Open Source Media Framework 播放器](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [客户端插入广告](media-services-inserting-ads-on-client-side.md)
### [授权 Microsoft 平滑流式处理客户端移植工具包](media-services-sspk.md)

## 集成
### [将 Azure Functions 与媒体服务配合使用](media-services-dotnet-how-to-use-azure-functions.md)
### [将 Azure Functions 与媒体服务配合使用的示例](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## 监视
### 检查作业进度
#### [REST](media-services-rest-check-job-progress.md)
#### [Azure portal](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [使用队列存储监视作业通知](media-services-dotnet-check-job-progress-with-queues.md)
### [使用 webhook 监视作业通知](media-services-dotnet-check-job-progress-with-webhooks.md)

## 故障排除
### [常见问题](media-services-frequently-asked-questions.md)
### [实时流式处理故障排除指南](media-services-troubleshooting-live-streaming.md)
### [错误代码](media-services-error-codes.md)
### [重试逻辑](media-services-retry-logic-in-dotnet-sdk.md)

# 引用
## [代码示例](https://azure.microsoft.com/en-us/resources/samples/?service=media-services)
## [Azure PowerShell（资源管理器）](/powershell/module/azurerm.media)
## [Azure PowerShell（服务管理）](/powershell/module/azure/?view=azuresmps-3.7.0)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST](/rest/api/media/mediaservice)  

# 资源
## [Azure 媒体服务社区](media-services-community.md)
## [Azure 路线图](https://azure.microsoft.com/roadmap/?category=web-mobile)
## [定价](https://azure.microsoft.com/pricing/details/media-services/)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)
## [发行说明](media-services-release-notes.md)
## [视频](https://azure.microsoft.com/resources/videos/index/?services=media-services)
