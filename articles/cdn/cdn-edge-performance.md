---
title: "分析 Azure CDN 中的边缘性能 | Microsoft Docs"
description: "分析 Microsoft Azure CDN 中的边缘节点性能。 “边缘性能分析”为 CDN 提供详尽的信息流量和带宽使用情况。"
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 61fe3f9419b5a6b15beb27d7b3f0782b404cd076


---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>分析 Microsoft Azure CDN 中的边缘节点性能
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>概述
“边缘性能分析”为 CDN 提供详尽的信息流量和带宽使用情况。 然后，此信息可用于生成趋势统计信息，以便你深入了解将你的资产缓存并传送到你的客户端的方式。 反过来，这允许你就如何优化内容传递和确定应解决的问题形成一种策略，以更好地利用 CDN。 因此，你不仅可以提高数据传输性能，还可以降低 CDN 成本。

> [!NOTE]
> 指定日期/时间时，所有报告都使用 UTC/GMT 表示法。
> 
> 

## <a name="reports-and-log-collection"></a>报告和日志收集
CDN 活动数据必须由“边缘性能分析”模块收集，然后才能为其生成报告。 此收集过程每天发生一次，并涵盖前一天发生的活动。 这意味着报告的统计信息表示当天的统计信息在处理时的样本，并且不一定包含当天的完整数据集。 这些报告的主要功能是评估性能。 不应将它们用于计费目的或精确的数字统计。

> [!NOTE]
> 生成“边缘性能分析”报告的原始数据至少在 90 天内可用。
> 
> 

## <a name="dashboard"></a>仪表板
“边缘性能分析”仪表板通过图表和统计信息跟踪当前和历史 CDN 流量。 使用此仪表板可以检测你帐户的 CDN 流量的最新和长期趋势。

此仪表板包括：

* 允许关键指标和趋势可视化的交互式图表。
* 一条时间线，提供关键指标和趋势的长期模式。
* 关键指标和关于我们的 CDN 网络如何提高站点流量（衡量标准是总体性能、使用情况和效率）的统计信息。

### <a name="accessing-the-edge-performance-dashboard"></a>访问边缘性能仪表板
1. 从 CDN 配置文件的边栏选项卡，单击“**管理**”按钮。
   
    ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    随即 CDN 管理门户打开。
2. 将鼠标悬停在“**分析**”选项卡，然后悬停在“**边缘性能分析**”浮出控件。  单击“**仪表板**”。
   
    将显示边缘节点分析仪表板。

### <a name="chart"></a>图表
仪表板包含一张图表，用于跟踪在其正下方显示的时间线中选择的时间段内的指标。  一条时间线将直接显示在图表下方，并以图表方式表示直到最近两年的 CDN 活动。

#### <a name="using-the-chart"></a>使用图表
* 默认情况下，过去 30 天的缓存效率比率将被绘制为图表。
* 此图表是根据每天收集的数据生成的。
* 将鼠标悬停在线形图上的某一天会显示日期和该日期的指标值。
* 单击“突出显示周末”可将表示周末的浅灰色垂直条覆盖到图表上。 这种类型的覆盖可用于识别周末期间的流量模式。
* 单击“查看一年前”可将同一时间段内上一年度的活动覆盖到图表上。 这种类型的比较可让我们深入了解长期的 CDN 使用模式。 图表的右上角包含一个图例，用于指示每个线形图的颜色代码。

#### <a name="updating-the-chart"></a>更新图表
* 时间范围：执行以下操作之一：
  * 在时间线中选择所需的区域。 图表将使用与所选时间段相对应的数据进行更新。
  * 双击图表以显示最多两年内的所有可用历史数据。
* 指标：单击所需指标旁边显示的图表图标。 图表和时间线将使用相应指标的数据进行刷新。

### <a name="key-metrics-and-statistics"></a>关键指标和统计信息
#### <a name="efficiency-metrics"></a>效率指标
使用这些指标的目的是查看是否可以提高缓存效率。 从缓存效率中获得的主要好处是：

* 减少源服务器上的负载，这可能导致：
  * 更好的 Web 服务器性能。
  * 降低运营成本。
* 改进的数据传输加速，因为将直接从 CDN 提供更多请求。

| 字段 | 说明 |
| --- | --- |
| 缓存效率 |表示从缓存中提供的数据的传输百分比。 该指标度量何时直接从 CDN（边缘服务器）向请求者（例如，Web 浏览器）提供所请求内容的缓存版本 |
| 命中率 |表示从缓存提供的请求的百分比。 该指标度量何时直接从 CDN（边缘服务器）向请求者（例如，Web 浏览器）提供所请求内容的缓存版本。 |
| % 的远程字节 - 无缓存配置 |表示从源服务器提供给 CDN（边缘服务器）的流量的百分比，由于“绕过缓存”功能（HTTP 规则引擎），该流量不会被缓存。 |
| % 的远程字节 - 到期缓存 |指示由于陈旧内容重新验证而从源服务器提供给 CDN（边缘服务器）的流量的百分比。 |

#### <a name="usage-metrics"></a>使用指标
使用这些指标的目的是为了让你深入了解以下降低成本的措施：

* 通过 CDN 将运营成本降至最低。
* 通过缓存效率和压缩减少 CDN 支出。

> [!NOTE]
> 流量数字代表在计算比率和百分比时使用的流量，并且可能只显示高流量客户的总流量的一部分。
> 
> 

| 字段 | 说明 |
| --- | --- |
| 平均传出字节 |表示从 CDN（边缘服务器）向请求者（例如，Web 浏览器）提供的每个请求传输的平均字节数。 |
| 无缓存配置字节率 |表示从 CDN（边缘服务器）向请求者（例如，Web 浏览器）提供的由于“绕过缓存”功能而不会被缓存的流量的百分比。 |
| 压缩字节率 |表示从 CDN（边缘服务器）发送到请求者（例如，Web 浏览器）的流量（压缩格式）的百分比。 |
| 传出字节 |表示从 CDN（边缘服务器）传递到请求者（例如，Web 浏览器）的数据量（以字节为单位）。 |
| 传入字节 |表示从请求者（例如，Web 浏览器）发送到 CDN（边缘服务器）的数据量（以字节为单位）。 |
| 远程字节 |表示从 CDN 和客户源服务器发送到 CDN（边缘服务器）的数据量（以字节为单位）。 |

#### <a name="performance-metrics"></a>性能指标
这些指标的目的是跟踪流量的整体 CDN 性能。

| 字段 | 说明 |
| --- | --- |
| 传输速率 |表示内容从 CDN 传输到请求者的平均速率。 |
| 持续时间 |表示将资产传送给请求者（例如，Web 浏览器）所需的平均时间（以毫秒为单位）。 |
| 压缩请求速率 |表示以压缩格式从 CDN（边缘服务器）传递到请求者（例如，Web 浏览器）的命中数的百分比。 |
| 4xx 错误率 |表示生成 4xx 状态代码的命中数的百分比。 |
| 5xx 错误率 |表示生成 5xx 状态代码的命中数的百分比。 |
| 命中数 |表示对 CDN 内容的请求数。 |

#### <a name="secure-traffic-metrics"></a>安全流量指标
这些指标的目的是跟踪 HTTPS 流量的 CDN 性能。

| 字段 | 说明 |
| --- | --- |
| 安全缓存效率 |表示从缓存提供的 HTTPS 请求传输的数据的百分比。 此指标度量何时通过 HTTPS 直接从 CDN（边缘服务器）向请求者（例如，Web 浏览器）提供所请求内容的缓存版本。 |
| 安全传输速率 |表示内容通过 HTTPS 从 CDN（边缘服务器）传输到请求者（例如，Web 服务器）的平均速率。 |
| 平均的安全持续时间 |表示通过 HTTPS 将资产传送给请求者（例如，Web 浏览器）所花费的平均时间（以毫秒为单位）。 |
| 安全命中数 |表示 CDN 内容的 HTTPS 请求数。 |
| 安全传出字节 |表示从 CDN（边缘服务器）传递到请求者（例如，Web 浏览器）的 HTTPS 流量（以字节为单位）。 |

## <a name="reports"></a>报告
此模块中的每个报告都包含有关不同类型指标的带宽和流量使用情况的图表和统计信息（例如，HTTP 状态代码，缓存状态代码，请求 URL 等）。 此信息可用于深入了解内容是如何提供给你的客户端以及如何微调 CDN 行为以提高数据传输性能。

### <a name="accessing-the-edge-performance-reports"></a>访问边缘性能报告
1. 从 CDN 配置文件的边栏选项卡，单击“**管理**”按钮。
   
    ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    随即 CDN 管理门户打开。
2. 将鼠标悬停在“**分析**”选项卡，然后悬停在“**边缘性能分析**”浮出控件。  单击“**HTTP 大型对象**”。
   
    将显示边缘节点分析报告屏幕。

| 报表 | 说明 |
| --- | --- |
| 每日摘要 |允许你查看指定时间段内的每日流量趋势。 此图表上的每个条形都表示特定的日期。 条形的大小表示在该日期发生的命中的总数。 |
| 每小时摘要 |允许你查看指定时间段内每小时的流量趋势。 此图表上的每个条形都表示特定日期的单个小时。 条形的大小表示在该小时内发生的命中的总数。 |
| 协议 |显示 HTTP 和 HTTPS 协议之间的流量细目。 环形图表示针对每种类型的协议发生的命中的百分比。 |
| HTTP 方法 |允许你快速了解正在使用哪些 HTTP 方法请求你的数据。 通常情况下，最常见的 HTTP 请求方法是 GET、HEAD 和 POST。 环形图表示针对每种类型的 HTTP 请求方法发生的命中的百分比。 |
| URL |包含一张显示前 10 个请求的 URL 的图表。 每个 URL 显示一个条形。 条的高度表示特定 URL 在报告涵盖的时间范围内生成了多少命中。 此图表下方直接显示前 100 个请求的 URL 的统计信息。 |
| CNAME |包含一张图表，其中显示在报告的时间范围内用于请求资产的前 10 个 CNAME。 此图表下方直接显示前 100 个请求的 CNAME 的统计信息。 |
| 来源 |包含一张图表，其中显示在指定时间段内从中请求资产的前 10 个 CDN 或客户源服务器。 此图表下方直接显示前 100 个请求的 CDN 或客户源服务器的统计信息。 客户源服务器通过“目录名称”选项中定义的名称进行标识。 |
| 地域 POP |显示通过特定的接入点 (POP) 进行路由的流量数。 三个字母的缩写表示我们的 CDN 网络中的 POP。 |
| 客户端 |包含一张图表，其中显示在指定时间段内请求资产的前 10 个客户端。 出于本报告的目的，来自同一 IP 地址的所有请求都被视为来自同一客户端。 此图表下方直接显示前 100 个客户端的统计信息。 此报告可用于确定最热门客户端的下载活动模式。 |
| 缓存状态 |提供缓存行为的明细，可揭示用于改进整体最终用户体验的方法。 缓存命中数越高，性能最高，因此你可以通过尽量降低缓存未命中数和过期的缓存命中数来优化数据传递速度。 |
| NONE 详细信息 |包含一张图表，其中显示在指定时间内未检查缓存内容新鲜度的资产的前 10 个 URL。 此图表下方直接显示这种类型的资产的前 100 个 URL的统计信息。 |
| CONFIG_NOCACHE 详细信息 |包含一张图表，其中显示由于客户的 CDN 配置而未缓存的资产的前 10 个URL。 这些类型的资产直接从源服务器提供。 此图表下方直接显示这种类型的资产的前 100 个 URL的统计信息。 |
| UNCACHEABLE 详细信息 |包含一张图表，其中显示由于请求标头数据而无法缓存的资产的前 10 个URL。 此图表下方直接显示这种类型的资产的前 100 个 URL的统计信息。 |
| TCP_HIT 详细信息 |包含一张图表，其中显示从缓存立即提供的资产的前 10 个 URL。 此图表下方直接显示这种类型的资产的前 100 个 URL的统计信息。 |
| TCP_MISS 详细信息 |包含一张图表，其中显示缓存状态为 TCP_MISS 的资产的前 10 个 URL。 此图表下方直接显示这种类型的资产的前 100 个 URL的统计信息。 |
| TCP_EXPIRED_HIT 详细信息 |包含一张图表，其中显示直接由 POP 提供的陈旧资产的前 10 个 URL。 此图表下方直接显示这种类型的资产的前 100 个 URL的统计信息。 |
| TCP_EXPIRED_MISS 详细信息 |包含一张图表，其中显示必须从源服务器检索新版本的过期资产的前 10 个URL。 此图表下方直接显示这种类型的资产的前 100 个 URL的统计信息。 |
| TCP_CLIENT_REFRESH_MISS 详细信息 |包含一个条形图，显示由于来自客户端的无缓存请求而从源服务器检索的资产的前 10 个 URL。 此图表下方直接显示这种类型的请求的前 100 个 URL的统计信息。 |
| 客户端请求类型 |表示 HTTP 客户端（例如，浏览器）发出的请求的类型。 此报告包括一个环形图，让你了解如何处理请求。 图表下方显示每个请求类型的带宽和流量信息。 |
| 用户代理 |包含一个条形图，其中显示前 10 个通过我们的 CDN 请求你的内容的用户代理。 通常情况下，用户代理是 Web 浏览器、媒体播放器或移动电话浏览器。 此图表下方直接显示前 100 个用户客户端的统计信息。 |
| 引用网站 |包含一个条形图，显示通过我们的 CDN 访问内容的前 10 个引用网站。 通常，引用网站是链接到你的内容的网页或资源的 URL。 在图表下方提供前 100 个引用网站的详细信息。 |
| 压缩类型 |包含一个环形图，其通过请求的资产是否被我们的边缘服务器压缩来分解它们。 按所使用的压缩类型细分压缩资产的百分比。 在图表下方提供每种压缩类型和状态的详细信息。 |
| 文件类型 |包含一个条形图，显示通过我们的 CDN 为你的帐户请求的前 10 个文件类型。 出于本报告的目的，文件类型由资产的文件扩展名和 Internet 媒体类型定义（例如 .html \[text/html\]、.htm \[text/html\]、.aspx \[text/html\] 等）。 在图表下方提供前 100 个文件类型的详细信息。 |
| 唯一的文件 |包含一张图表，用于绘制在指定时间段的特定日期内请求的唯一资产总数。 |
| 令牌身份验证摘要 |包含一个饼图，快速概述了请求的资产是否受到“基于令牌的身份验证”的保护。 受保护的资产将根据其尝试身份验证的结果显示在图表中。 |
| 令牌身份验证拒绝的详细信息 |包含一个条形图，允许你查看由于“基于令牌的身份验证”而被拒绝的前 10 个请求。 |
| HTTP 响应代码 |提供由我们的边缘服务器传递到你的 HTTP 客户端的 HTTP 状态代码（例如，200 OK、403 Forbidden、404 Not Found 等）的细分。 饼图可让你快速评估资产的提供方式。 在图表的下方提供每个响应代码的详细统计数据。 |
| 404 错误 |包含一个条形图，允许你查看导致 404 Not Found 响应代码的前 10 个请求。 |
| 403 错误 |包含一个条形图，允许你查看导致 403 Forbidden 响应代码的前 10 个请求。 当客户源服务器或 POP 上的边缘服务器拒绝请求时，会出现 403 Forbidden 响应代码。 |
| 4xx 错误 |包含一个条形图，允许你查看导致 400 范围内响应代码的前 10 个请求。 此报告中排除 403 Not Found 和404 Forbidden 响应代码。 通常情况下，当由于客户端错误而拒绝请求时，会出现 4xx 响应代码。 |
| 504 错误 |包含一个条形图，允许你查看导致 504 Gateway Timeout 响应代码的前 10 个请求。 当 HTTP 代理尝试与另一个服务器通信时发生超时，将出现 504 Gateway Timeout 响应代码。 在我们的 CDN 中，当边缘服务器不能与客户源服务器的建立通信时，通常出现 504 Gateway Timeout 响应代码。 |
| 502 错误 |包含一个条形图，允许你查看导致 502 Bad Gateway 响应代码的前 10 个请求。 当服务器和 HTTP 代理之间发生 HTTP 协议故障时，会出现 502 Bad Gateway 响应代码。 在我们的 CDN 中，当客户源服务器向边缘服务器返回无效响应时，通常出现发生 502 Bad Gateway 响应代码。 如果响应无法解析或不完整，则该响应无效。 |
| 5xx 错误 |包含一个条形图，允许你查看导致 500 范围内响应代码的前 10 个请求。  此报告中排除 502 Bad Gateway 和 504 Gateway Timeout 响应代码。 |

## <a name="see-also"></a>另请参阅
* [Azure CDN 概述](cdn-overview.md)
* [Microsoft Azure CDN 中的实时统计信息](cdn-real-time-stats.md)
* [使用规则引擎重写默认 HTTP 行为](cdn-rules-engine.md)
* [高级 HTTP 报告](cdn-advanced-http-reports.md)




<!--HONumber=Nov16_HO3-->

