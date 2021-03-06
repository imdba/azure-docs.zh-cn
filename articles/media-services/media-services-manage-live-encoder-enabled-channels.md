---
title: "使用 Azure 媒体服务执行实时传送视频流以创建多比特率流 | Microsoft Docs"
description: "本主题介绍如何设置频道，以从本地编码器接收单比特率实时流，并使用媒体服务执行实时编码以将其转换为自适应比特率流。 然后，该流可使用以下自适应流式传输协议之一，通过一个或多个流式传输终结点传送给客户端播放应用程序：HLS、平滑流、MPEG DASH。"
services: media-services
documentationcenter: 
author: anilmur
manager: cfowler
editor: 
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;anilmur
ms.openlocfilehash: d5f76d532b236e67a4e69eb820e2cfc3033a80c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>使用 Azure 媒体服务执行实时流式处理以创建多比特率流
## <a name="overview"></a>概述
在 Azure 媒体服务 (AMS) 中，**频道**表示用于处理实时传送视频流内容的管道。 **频道**通过以下两种方式之一接收实时输入流：

* 本地实时编码器（采用以下格式之一：RTP (MPEG-TS)、RTMP 或平滑流式处理（分片 MP4））将单比特率流发送至能够使用媒体服务执行实时编码的频道。 然后，频道将对传入的单比特率流执行实时编码，使之转换为多比特率（自适应）视频流。 收到请求时，媒体服务会将该流传递给客户。
* 本地实时编码器将多比特率 **RTMP** 或**平滑流式处理**（零碎的 MP4）发送到无法通过 AMS 进行实时编码的频道。 引入流将通过**频道**，而不会进行任何进一步处理。 这种方法称为**直通**。 可以使用以下输出多比特率平滑流式处理的实时编码器：MediaExcel、Ateme、Imagine Communications、Envivio、Cisco 和 Elemental。 以下实时编码器输出 RTMP：Adobe Flash Media Live Encoder (FMLE)、Telestream Wirecast、Haivision、Teradek 和 Tricaster 编码器。  实时编码器也可将单比特率流发送到并未启用实时编码的频道，并不建议这样做。 收到请求时，媒体服务会将该流传递给客户。
  
  > [!NOTE]
  > 实时传送视频流时，使用直通方法是最经济的。
  > 
  > 

从媒体服务2.10 发行版开始，在创建频道时，可以指定你想要频道接收输入流的方式，以及是否想要频道对流执行实时编码。 可以使用两个选项：

* **无** - 如果计划使用输出多比特率流（直通流）的本地实时编码器，请指定此值。 在这种情况下，传入流将传递到输出，而不会进行任何编码。 这是 2.10 发行版以前的频道行为。  有关使用此类型频道的详细信息，请参阅[使用创建多比特率流的本地编码器执行实时传送视频流](media-services-live-streaming-with-onprem-encoders.md)。
* **标准** - 如果计划使用媒体服务将单比特率实时流编码为多比特率流，请选择此值。 请注意，实时编码会影响计费，应该记住，将实时编码通道保持为“正在运行”状态会产生费用。  建议在实时流式处理事件完成之后立即停止正在运行的通道，以避免产生额外的小时费用。

> [!NOTE]
> 本主题讨论为执行实时编码（**标准**编码类型）启用的频道的属性。 若要了解如何使用无法执行实时编码的频道，请参阅[使用创建多比特率流的本地编码器执行实时传送视频流](media-services-live-streaming-with-onprem-encoders.md)。
> 
> 请务必仔细阅读[注意事项](media-services-manage-live-encoder-enabled-channels.md#Considerations)部分。
> 
> 

## <a name="billing-implications"></a>计费影响
一旦通过 API 将实时编码通道的状态转换为“正在运行”，就会开始计费。   也可以在 Azure 门户或 Azure 媒体服务资源管理器工具 (http://aka.ms/amse) 中查看状态。

下表显示了频道状态如何映射到 API 和 Azure 门户中的计费状态。 请注意，API 与门户 UX 之间的状态略有不同。 通过 API 将通道置于“正在运行”状态，或者在 Azure 门户中将它设置为“就绪”或“正在流式处理”状态后，就会开始计费。
若要阻止频道进一步计费，则必须通过 API 或 Azure 门户停止频道。
在使用完实时编码通道时，需要亲自停止通道。  不停止编码通道会导致持续计费。

### <a id="states"></a>频道状态及其如何映射到计费模式
频道的当前状态。 可能的值包括：

* **已停止**。 这是通道在创建后的初始状态（除非在门户中选择了自动启动）。此状态下不会发生计费。 在此状态下，可以更新频道属性，但不允许进行流式传输。
* **正在启动**。 频道正在启动。 此状态下不会发生计费。 在此状态下，不允许进行更新或流式传输。 如果发生错误，则频道会返回到“已停止”状态。
* **正在运行**。 频道能够处理实时流。 现在会计收使用费。 必须停止通道以防止进一步计费。 
* **正在停止**。 频道正在停止。 此暂时性状态下不会发生计费。 在此状态下，不允许进行更新或流式传输。
* **正在删除**。 频道正被删除。 此暂时性状态下不会发生计费。 在此状态下，不允许进行更新或流式传输。

下表显示频道状态如何映射到计费模式。 

| 频道状态 | 门户 UI 指示器 | 是否计费？ |
| --- | --- | --- |
| 正在启动 |正在启动 |否（暂时状态） |
| 正在运行 |准备就绪（没有正在运行的节目）<br/>或<br/>流式处理（至少有一个正在运行的节目） |是 |
| 正在停止 |正在停止 |否（暂时状态） |
| 已停止 |已停止 |否 |

### <a name="automatic-shut-off-for-unused-channels"></a>对未使用的通道自动关闭
从 2016 年 1 月 25 日开始，媒体服务推出了一项更新，对于长时间以未使用状态运行的通道，它会自动停止该通道（已启用实时编码）。 这适用于没有活动节目且长时间未收到输入贡献源的通道。

未使用期限的阈值通常为 12 个小时，但随时会变化。

## <a name="live-encoding-workflow"></a>实时编码工作流
下图表示了实时流式处理工作流，其中频道通过以下协议之一接收单比特率流：RTMP、平滑流式处理或 RTP (MPEG-TS)；然后，将该流编码为多比特率流。 

![实时工作流][live-overview]

## <a id="scenario"></a>常见的实时流方案
以下是在创建常见的实时流应用程序时涉及的常规步骤。

> [!NOTE]
> 目前，实时事件的最大建议持续时间为 8 小时。 如果需要长时间运行某个频道，请通过 Microsoft.com 联系 amslived。请注意，实时编码会影响计费，且应记住将实时编码频道保持为“正在运行”状态会产生以小时计算的费用。  建议在实时流式处理事件完成之后立即停止正在运行的通道，以避免产生额外的小时费用。 
> 
> 

1. 将视频摄像机连接到计算机。 启动并配置可以通过以下协议之一输出**单**比特率流的本地实时编码器：RTMP、平滑流式处理或 RTP (MPEG-TS)。 
   
    此步骤也可以在创建频道后执行。
2. 创建并启动频道。 
3. 检索频道引入 URL。 
   
    实时编码器使用引入 URL 将流发送到频道。
4. 检索频道预览 URL。 
   
    使用此 URL 来验证频道是否正常接收实时流。
5. 创建节目。 
   
    使用 Azure 门户时，创建节目的同时还会创建资产。 
   
    使用 .NET SDK 或 REST 时，需要创建一个资源并指定在创建节目时要使用该资源。 
6. 发布与节目关联的资源。   
   
    >[!NOTE]
    >创建 AMS 帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。 要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。 
    
7. 在准备好开始流式传输和存档时，启动节目。
8. （可选）可以向实时编码器发信号，以启动广告。 将广告插入到输出流中。
9. 在要停止对事件进行流式传输和存档时，停止节目。
10. 删除节目（并选择性地删除资产）。   

> [!NOTE]
> 千万不要忘记停止实时编码通道。 请注意，实时编码会影响每小时计费，应该记住，将实时编码通道保持为“正在运行”状态会产生费用。  建议在实时流式处理事件完成之后立即停止正在运行的通道，以避免产生额外的小时费用。 
> 
> 

## <a id="channel"></a>通道输入（引入）配置
### <a id="Ingest_Protocols"></a>引入流式传输协议
如果“编码器类型”设为“标准”，则有效选项为：

* **RTP** (MPEG-TS)：RTP 上的 MPEG-2 传输流。  
* 单比特率 **RTMP**
* 单比特率**零碎的 MP4**（平滑流式处理）

#### <a name="rtp-mpeg-ts---mpeg-2-transport-stream-over-rtp"></a>RTP (MPEG-TS) - RTP 上的 MPEG-2 传输流。
典型用例： 

专业广播者通常使用供应商提供的高端本地实时编码器（如 Elemental Technologies、Ericsson、Ateme、Imagine 或 Envivio）来发送流。 通常与 IT 部门和专用网络配合使用。

注意事项：

* 强烈建议使用单节目传输流 (SPTS) 输入。 
* 使用 MPEG-2 TS over RTP 最多可以输入 8 个音频流。 
* 视频流应具有小于 15 Mbps 的平均比特率
* 音频流的聚合平均比特率应小于 1 Mbps
* 以下是支持的编解码器：
  
  * MPEG-2/H.262 Video 
    
    * Main Profile (4:2:0)
    * High Profile (4:2:0, 4:2:2)
    * 422 Profile (4:2:0, 4:2:2)
  * MPEG-4 AVC/H.264 Video  
    
    * Baseline、Main、High Profile（8 位 4:2:0）
    * High 10 Profile（10 位 4:2:0）
    * High 422 Profile（10 位 4:2:2）
  * MPEG-2 AAC-LC Audio 
    
    * Mono、Stereo、Surround (5.1, 7.1)
    * MPEG-2 样式 ADTS 打包
  * Dolby Digital (AC-3) Audio 
    
    * Mono、Stereo、Surround (5.1, 7.1)
  * MPEG Audio（层 II 和层 III） 
    
    * Mono、Stereo
* 建议的广播编码器包括：
  
  * Imagine Communications Selenio ENC 1
  * Imagine Communications Selenio ENC 2
  * Elemental Live

#### <a id="single_bitrate_RTMP"></a>单比特率 RTMP
注意事项：

* 传入流不能包含多码率视频
* 视频流应具有小于 15 Mbps 的平均比特率
* 音频流应具有小于 1 Mbps 的平均比特率
* 以下是支持的编解码器：
* MPEG-4 AVC/H.264 Video
* Baseline、Main、High Profile（8 位 4:2:0）
* High 10 Profile（10 位 4:2:0）
* High 422 Profile（10 位 4:2:2）
* MPEG-2 AAC-LC Audio
* Mono、Stereo、Surround (5.1, 7.1)
* 44.1 kHz 采样率
* MPEG-2 样式 ADTS 打包
* 建议的编码器包括：
* Telestream Wirecast
* Flash 媒体实时编码器

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>单比特率分片 MP4（平滑流）
典型用例：

使用供应商提供的本地实时编码器（如 Elemental Technologies、Ericsson、Ateme、Envivio）通过开放的 Internet 将输入流发送到附近的 Azure 数据中心。

注意事项：

这同样适用于[单比特率 RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP)。

#### <a name="other-considerations"></a>其他注意事项
* 当频道或其关联的节目正在运行时，无法更改输入协议。 如果需要不同的协议，应当针对每个输入协议创建单独的频道。
* 传入视频流的最大分辨率为 1920 x 1080，如果隔行扫描，速率最大为 60 个字段/秒，如果渐进式下载，则速度为 30 帧/秒。

### <a name="ingest-urls-endpoints"></a>摄取 URL（终结点）
频道提供你在实时编码器中指定的输入终结点（引入 URL），因此编码器可以将流推送到你的频道。

创建频道后，可以获得引入 URL。 若要获取这些 URL，频道不一定要处于“正在运行”状态。 准备好开始将数据推送到频道时，频道必须处于“正在运行”状态。 在频道开始引入数据后，可以通过预览 URL 来预览流。

可以选择通过 SSL 连接引入分片 MP4（平滑流）实时流。 要通过 SSL 进行摄取，请确保将摄取 URL 更新为 HTTPS。 请注意，目前 AMS 对自定义域不支持 SSL。  

### <a name="allowed-ip-addresses"></a>允许的 IP 地址
可以定义允许向此频道发布视频的 IP 地址。 允许的 IP 地址可以指定为单个 IP 地址（例如“10.0.0.1”）、使用一个 IP 地址和 CIDR 子网掩码的 IP 范围（例如“10.0.0.1/22”），或使用一个 IP 地址和点分十进制子网掩码的 IP 范围（例如“10.0.0.1(255.255.252.0)”）。

如果未指定 IP 地址并且没有规则定义，则不会允许任何 IP 地址。 若要允许任何 IP 地址，请创建一个规则并设置 0.0.0.0/0。

## <a name="channel-preview"></a>频道预览
### <a name="preview-urls"></a>预览 URL
频道还提供了一个预览终结点（预览 URL），可以使用它在进一步处理和传递流之前预览流并对其进行验证。

可以在创建频道时获取预览 URL。 若要获取该 URL，频道不一定要处于“正在运行”状态。

在频道开始摄取数据后，可以预览流。

> [!NOTE]
> 当前，不管指定了哪种输入类型，都只能以分片 MP4（平滑流）格式来传送预览流。 可以使用 [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) 播放器来测试平滑流。 还可以使用 Azure 门户中托管的播放器来查看流。
> 
> 

### <a name="allowed-ip-addresses"></a>允许的 IP 地址
可以定义允许连接到预览终结点的 IP 地址。 如果未指定 IP 地址，则将允许任何 IP 地址。 允许的 IP 地址可以指定为单个 IP 地址（例如“10.0.0.1”）、使用一个 IP 地址和 CIDR 子网掩码的 IP 范围（例如“10.0.0.1/22”），或使用一个 IP 地址和点分十进制子网掩码的 IP 范围（例如“10.0.0.1(255.255.252.0)”）。

## <a name="live-encoding-settings"></a>实时编码设置
本部分介绍当频道的“编码类型”设为“标准”时，如何调整频道内的实时编码器的设置。

> [!NOTE]
> 在 Azure 中输入多个语言轨迹和执行实时编码时，多语言输入仅支持 RTP。 使用 MPEG-2 TS over RTP 最多可以定义 8 个音频流。 当前不支持使用 RTMP 或平滑流引入多个音频轨迹。 使用[本地实时编码](media-services-live-streaming-with-onprem-encoders.md)执行实时编码时，不存在这种限制，因为发送到 AMS 的任何数据都会通过频道，而不做进一步的处理。
> 
> 

### <a name="ad-marker-source"></a>Ad 标记源
可以指定 Ad 标记信号源。 默认值是 **Api**，它指示频道内的实时编码器应侦听异步 **Ad 标记 API**。

另一个有效选项是 **Scte35**（仅当引入流式处理协议设为 RTP (MPEG-TS) 时允许）。 当指定了 Scte35 时，实时编码器将分析输入 RTP (MPEG-TS) 流中的 SCTE-35 信号。

### <a name="cea-708-closed-captions"></a>CEA 708 关闭字幕
一个可选的标志，它指示实时编码器忽略传入视频中嵌入的任何 CEA 708 字幕数据。 当该标志设为 false（默认值）时，编码器将检测 CEA 708 数据并将该数据重新插入到输出视频流中。

### <a name="video-stream"></a>视频流
可选。 描述输入视频流。 如果未指定此字段，则使用默认值。 仅当输入流协议设为 RTP (MPEG-TS) 时，才允许使用此设置。

#### <a name="index"></a>索引
一个从零开始的索引，它指定哪个输入视频流应由频道内的实时编码器处理。 仅当引入流协议是 RTP (MPEG-TS) 时，此设置才适用。

默认值为 0。 建议在单节目传输流 (SPTS) 中发送。 如果输入流包含多个节目，实时编码器将分析输入中的节目映射表 (PMT)，标识流类型名称为 MPEG-2 Video 或 H.264 的输入并以 PMT 中指定的顺序安排这些输入。 然后，将使用从零开始的索引选取该安排中的第 n 个条目。

### <a name="audio-stream"></a>音频流
可选。 描述输入音频流。 如果未指定此字段，则将应用指定的默认值。 仅当输入流协议设为 RTP (MPEG-TS) 时，才允许使用此设置。

#### <a name="index"></a>索引
建议在单节目传输流 (SPTS) 中发送。 如果输入流包含多个节目，频道中的实时编码器将分析输入中的节目映射表 (PMT)，标识流类型名称为 MPEG-2 AAC ADTS、AC-3 System-A、AC-3 System-B、MPEG-2 Private PES、MPEG-1 Audio 或 MPEG-2 Audio 的输入并以 PMT 中指定的顺序安排这些输入。 然后，将使用从零开始的索引选取该安排中的第 n 个条目。

#### <a name="language"></a>语言
音频流的符合 ISO 639-2 的语言标识符，如 ENG。 如果不存在，则默认为 UND（未定义）。

如果频道的输入为 MPEG-2 TS over RTP，则最多可以指定 8 个音频流集。 但是，不能有两个具有相同索引值的条目。

### <a id="preset"></a>系统预设
指定此频道内的实时编码器要使用的预设。 目前，唯一允许的值是 **Default720p**（默认值）。

请注意，如果需要自定义预设，应通过 Microsoft.com 联系 amslived。

**Default720p** 会将视频编码为以下 7 层。

#### <a name="output-video-stream"></a>输出视频流
| 比特率 | 宽度 | 高度 | MaxFPS | 配置文件 | 输出流名称 |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |主要 |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |主要 |Video_704x396_1350kbps |
| 850 |512 |288 |30 |主要 |Video_512x288_850kbps |
| 550 |384 |216 |30 |主要 |Video_384x216_550kbps |
| 350 |340 |192 |30 |基线 |Video_340x192_350kbps |
| 200 |340 |192 |30 |基线 |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>输出音频流
音频以 64 kbps 的速率编码为立体声 AAC-LC，采样率为 44.1 kHz。

## <a name="signaling-advertisements"></a>指示广告
当频道已启用实时编码时，在管道中有一个组件正在处理视频，并可以操作它。 可以向频道发出信号，以将清单和/或广告插入到传出自适应比特率流中。 清单是在某些情况下（例如，在商业广告期间）可用于覆盖输入实时源的静止图像。 广告信号是嵌入到传出流中的时间同步信号，用于指示视频播放机执行特殊操作（例如，在适当时间切换到广告）。 有关用于此目的的 SCTE-35 信号机制的概述，请参阅此[博客](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/)。 下面是可以在实时事件中实现的典型方案。

1. 在事件开始前，让观看者获得事件前图像。
2. 在事件结束后，让观看者获得事件后图像。
3. 如果在事件期间出现问题（例如，在体育场中出现电源故障，让观看者获得错误事件图像。
4. 在商业广告期间发送广告时间图像以隐藏实时事件源。

以下是指示广告时可以设置的属性。 

### <a name="duration"></a>持续时间
商业广告的持续时间（以秒为单位）。 这必须是非零正值，才能启动商业广告。 当商业广告正在播放时，将持续时间设为 0，并且 CueId 与正在播放的商业广告匹配，则广告会被取消。

### <a name="cueid"></a>CueId
商业广告的唯一 ID，下游应用程序将使用它来执行相应操作。 必须是一个正整数。 可以将此值设为任意随机正整数，或使用上游系统跟踪提示 ID。 在通过 API 提交之前，请确保将任何 ID 规范化为正整数。

### <a name="show-slate"></a>显示清单
可选。 指示实时编码器在商业广告期间切换到[默认盖板](media-services-manage-live-encoder-enabled-channels.md#default_slate)图像并隐藏传入视频源。 在清单期间音频也会静音。 默认值为 **false**。 

所用图像将是在创建频道时通过默认清单资源 ID 属性指定的图像。 静态图像将进行拉伸以适合显示图像大小。 

## <a name="insert-slate--images"></a>插入“盖板”图像
可以向频道中的实时编码器发出信号，以切换到清单图像。 它还可以指示实时编码器结束正在显示的清单。 

在某些情况下（例如，在广告期间）可以配置实时编码器以切换到清单图像，并隐藏传入的视频信号。 如果未配置此类清单，则在该广告期间将不会屏蔽输入视频。

### <a name="duration"></a>Duration
清单的持续时间（以秒为单位）。 这必须是非零正值，才能启动清单。 如果正在显示清单，并且指定的持续时间为零，则将终止正在显示的清单。

### <a name="insert-slate-on-ad-marker"></a>在 Ad 标记上插入清单
当设为 true 时，此设置会将实时编码器配置为在广告期间插入清单图像。 默认值为 true。 

### <a id="default_slate"></a>默认静态图像资产 ID

可选。 指定媒体服务资源（包含清单图像）的资源 ID。 默认值为 null。 


>[!NOTE] 
>在创建频道之前，具有以下约束的盖板图像应当作为专用资产上传（该资产中不应有其他文件）。 只有在广告时间，或已明确收到信号要求插入盖板时，实时编码器才会插入盖板，此时才会使用此图像。 实时编码器在某些错误条件下也会转到盖板模式 - 例如输入信号丢失时。 实时编码器进入这种“输入信号丢失”状态时，目前无法使用自定义图像。 可在[此处](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel)建议提供此功能。


* 分辨率最大为 1920x1080。
* 大小最大为 3 MB。
* 文件名必须具有 *.jpg 扩展名。
* 必须将该图像作为资产中的唯一 AssetFile 上传到资产，此 AssetFile 应标记为主文件。 资产不能加密存储。

如果未指定“默认盖板资产 Id”，并且“在 ad 标记上插入盖板”设为 **true**，则将使用默认 Azure 媒体服务图像隐藏输入视频流。 在清单期间音频也会静音。 

## <a name="channels-programs"></a>频道的节目
频道与节目相关联，使用节目，可以控制实时流中的段的发布和存储。 通道管理节目。 通道和节目的关系非常类似于传统媒体，通道具有恒定的内容流，而节目的范围限定为该通道上的一些定时事件。

可以通过设置 **存档窗口** 长度，指定希望保留节目录制内容的小时数。 此值的设置范围是最短 5 分钟，最长 25 小时。 存储时间窗口长度还决定了客户端能够从当前实时位置按时间向后搜索的最长时间。 超出指定时间长度后，节目也能够运行，但落在时间窗口长度后面的内容将全部被丢弃。 此属性的这个值还决定了客户端清单能够增加多长时间。

每个节目都与存储流式处理内容的资源相关联。 资产映射到 Azure 存储帐户中的 blob 容器，资产中的文件作为 blob 存储在该容器中。 若要发布节目，以便客户可以查看该流，必须为关联的资源创建按需定位符。 创建此定位符后，可以生成提供给客户端的流 URL。

一个频道最多支持三个同时运行的节目，因此可以为同一传入流创建多个存档。 这样，便可以根据需要发布和存档事件的不同部分。 例如，业务要求是存档 6 小时的节目，但只广播过去 10 分钟的内容。 为了实现此目的，需要创建两个同时运行的节目。 一个节目设置为存档 6 小时的事件但不发布该节目。 另一个节目设置为存档 10 分钟的事件，并且要发布该节目。

不应当将现有节目重用于新事件。 而是应当为每个事件创建并启动一个新节目，如“对实时流式传输应用程序进行编程”部分中所述。

在准备好开始流式传输和存档时，启动节目。 在要停止对事件进行流式传输和存档时，停止节目。 

要删除存档的内容，请停止并删除节目，并删除关联的资产。 如果资产被某个节目使用，则无法将其删除，必须先删除该节目。 

即使你停止并删除了节目，只要没有删除资产，用户也能够按需将已存档内容作为视频进行流式传输。

如果希望保留已存档的内容但不希望其可供流式传输，请删除流式传输定位符。

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>获取实时源的缩略图预览
启用实时编码后，现在可以在实时源到达频道时获得实时源的预览。 这可以是一个很有用的工具，用于检查实时源是否实际到达频道。 

## <a id="states"></a>通道状态，以及状态如何映射到计费模式
频道的当前状态。 可能的值包括：

* **已停止**。 这是频道在创建后的初始状态。 在此状态下，可以更新频道属性，但不允许进行流式传输。
* **正在启动**。 频道正在启动。 在此状态下，不允许进行更新或流式传输。 如果发生错误，则频道会返回到“已停止”状态。
* **正在运行**。 频道能够处理实时流。
* **正在停止**。 频道正在停止。 在此状态下，不允许进行更新或流式传输。
* **正在删除**。 频道正被删除。 在此状态下，不允许进行更新或流式传输。

下表显示频道状态如何映射到计费模式。 

| 频道状态 | 门户 UI 指示器 | 是否计费？ |
| --- | --- | --- |
| 正在启动 |正在启动 |否（暂时状态） |
| 正在运行 |准备就绪（没有正在运行的节目）<br/>或<br/>流式处理（至少有一个正在运行的节目） |是 |
| 正在停止 |正在停止 |否（暂时状态） |
| 已停止 |已停止 |否 |

> [!NOTE]
> 目前，启动通道所需的平均时间为大约 2 分钟，最长可能需要 20 多分钟。 通道重置最长可能需要 5 分钟。
> 
> 

## <a id="Considerations"></a>注意事项
* 当某个编码类型为“标准”的频道出现输入源/贡献源丢失的情况时，该频道会采取相应的补偿措施，将源视频/音频替换为错误的盖板和静音。 该频道会持续发出静态图像，直到输入/贡献源恢复。 我们建议不要让实时频道处于此类状态的时间超过 2 小时。 如果超出该限制，该频道将无法保证输入重新连接时的行为，也无法保证其响应重置命令时的行为。 这种情况下必须停止通道并将其删除，然后创建一个新的。
* 当频道或其关联的节目正在运行时，无法更改输入协议。 如果需要不同的协议，应当针对每个输入协议创建单独的频道。
* 每次重新配置实时编码器时，可调用频道上的**重置**方法。 在重置频道之前，必须停止节目。 在重置频道后，重新启动节目。
* 只有当频道处于“正在运行”状态且频道中的所有节目都已停止时才能停止频道。
* 默认情况下，只能向媒体服务帐户添加 5 个频道。 这是所有新帐户的软配额。 有关详细信息，请参阅[配额和限制](media-services-quotas-and-limitations.md)。
* 当频道或其关联的节目正在运行时，无法更改输入协议。 如果需要不同的协议，应当针对每个输入协议创建单独的频道。
* 仅当频道处于“正在运行”状态时才会向你收费。 有关详细信息，请参阅[此](media-services-manage-live-encoder-enabled-channels.md#states)部分。
* 目前，实时事件的最大建议持续时间为 8 小时。 如果需要较长时间运行某个频道，请通过 Microsoft.com 联系 amslived。
* 确保使要从中流式传输内容的流式处理终结点处于“正在运行”状态。
* 在 Azure 中输入多个语言轨迹和执行实时编码时，多语言输入仅支持 RTP。 使用 MPEG-2 TS over RTP 最多可以定义 8 个音频流。 当前不支持使用 RTMP 或平滑流引入多个音频轨迹。 使用[本地实时编码](media-services-live-streaming-with-onprem-encoders.md)执行实时编码时，不存在这种限制，因为发送到 AMS 的任何数据都会通过频道，而不做进一步的处理。
* 编码预设使用“最大帧速率”30 fps 的思路。 因此，如果输入为 60fps/59.97i，则输入帧将修剪/反交错为 30/29.97 fps。 因此，如果输入为 50fps/50i，则输入帧将修剪/反交错为 25 fps。 如果输入为 25 fps，则输出将保持为 25 fps。
* 完成后请不要忘记关闭通道。 否则会继续计费。

## <a name="known-issues"></a>已知问题
* 通道启动时间已改善为平均 2 分钟，但有时因为需求提高，可能仍然需要长达 20 分钟以上的时间。
* RTP 支持迎合专业的广播装置。 请查看[此](https://azure.microsoft.com/blog/2015/04/13/an-introduction-to-live-encoding-with-azure-media-services/)博客中有关 RTP 的说明。
* 盖板图像应符合[此处](media-services-manage-live-encoder-enabled-channels.md#default_slate)所述的限制。 如果想要尝试创建默认静态图像大于 1920x1080 的频道，最终请求会出错。
* 再次强调，完成流式处理后请不要忘记关闭通道。 否则会继续计费。

## <a name="next-step"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>相关主题
[使用 Azure 媒体服务传送实时传送视频流事件](media-services-overview.md)

[创建通道，通过门户执行从单比特率到自适应比特率流的实时编码](media-services-portal-creating-live-encoder-enabled-channel.md)

[创建通道，通过 NET SDK 执行从单比特率到自适应比特率流的实时编码](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[通过 REST API 管理通道](https://docs.microsoft.com/rest/api/media/operations/channel)
 
[媒体服务概念](media-services-concepts.md)

[Azure 媒体服务零碎的 MP4 实时引入规范](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

