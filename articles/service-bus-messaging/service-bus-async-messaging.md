---
title: "服务总线异步消息传送 | Microsoft 文档"
description: "介绍 Azure 服务总线异步消息传送。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: d36360f3fb46adf96f53976584987590791b07d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>异步消息传送模式和高可用性

可以通过多种不同的方式实现异步消息传送。 对于队列、主题和订阅，Azure 服务总线通过存储和转发机制支持异步。 在正常（同步）操作中，会将消息发送到队列和主题，并从队列和主题接收消息。 编写的应用程序依赖于这些始终可用的实体。 当实体运行状况因各种环境而发生变化时，需要一种能够提供满足大多数需求的缩减功能实体的方式。

应用程序通常使用异步消息传送模式来实现大量通信方案。 可以构建一些应用程序，以便客户端在其中可以向服务发送消息（即使该服务未运行）。 对于将经历大量通信的应用程序，队列可以通过提供缓冲通信的场所，帮助对负载进行分级。 最后，可以获得一个简单而高效的负载均衡器，从而在多台计算机间分发消息。

为了维护任何这些实体的可用性，请考虑表达这些实体可能不可用的多种方式，从而构建持久的消息传送系统。 一般而言，发现实体对应用程序不可用时，有以下表达方式：

* 无法发送消息。
* 无法接收消息。
* 无法管理实体（创建、检索、更新或删除实体）。
* 无法与服务取得联系。

对于以上每种故障，存在不同的故障模式，从而使应用程序能够在某种程度功能缩减的情况下继续执行工作。 例如，可以发送消息但无法接收消息的系统仍可以从客户接收指令，但无法处理这些指令。 本主题讨论可能发生的潜在问题以及如何缓解这些问题。 服务总线引入了许多必须采用的缓解措施。此外本主题还将讨论管理使用这些加入的缓解措施的规则。

## <a name="reliability-in-service-bus"></a>服务总线可靠性
可通过多种方式来处理消息和实体问题，有一套对这些缓解措施的恰当使用进行管理的准则。 要了解这些准则，必须先了解服务总线中可能出现的故障。 由于 Azure 系统的设计，所有这些故障往往都是短期的。 在高级别中，引起不可用的各种原因如下所示：

* 来自服务总线所依赖的外部系统的限制。 与存储和计算资源的交互存在限制。
* 服务总线所依赖的系统出现问题。 例如，存储的给定部分可能遇到问题。
* 单个子系统上出现服务总线故障。 在此情况下，计算节点可能会陷入不一致状态而必须重新启动其自身，从而导致它负责处理的所有实体负载均衡到其他节点。 这又可能导致短时间内消息处理变慢。
* Azure 数据中心内的服务总线故障。 这是“灾难性故障”，无论故障时间是数分钟还是几小时，在此期间都无法访问系统。

> [!NOTE]
> “存储”这一术语既能表示 Azure 存储又能表示 SQL Azure。
> 
> 

服务总线包含了针对这些问题的许多缓解措施。 以下各节介绍了每个问题及其相应的缓解措施。

### <a name="throttling"></a>限制
通过服务总线，设置限制可以实现协作消息速率管理。 每个单独的服务总线节点包含许多实体。 其中每个实体都需要在 CPU、内存、存储和其他方面占用系统。 当上述任一方面检测到超出定义阈值的使用情况时，服务总线可以拒绝给定的请求。 调用方会接收到 [ServerBusyException][ServerBusyException]，并在 10 秒后重试。

作为一种缓解措施，该代码必须读取错误并停止该消息的任何重试至少 10 秒。 由于此错误可能发生在多个客户应用程序之间，所以最好使每个应用程序独立执行重试逻辑。 该代码可以通过对队列或主题启用分区来减少受限概率。

### <a name="issue-for-an-azure-dependency"></a>Azure 依赖项的问题
Azure 中的其他组件可能偶尔会发生服务问题。 例如，当服务总线使用的系统正在升级时，该系统可能会暂时出现功能缩减。 为了解决这些类型的问题，服务总线会定期进行调查并实施缓解措施。 这些缓解措施的副作用的确存在。 例如，为了处理存储的暂时性问题，服务总线将实现系统来允许消息发送操作持续工作。 由于缓解措施的性质，已发送的消息可能需要最多 15 分钟的时间才能出现在受影响的队列或订阅中和准备好进行接收操作。 一般而言，大多数实体不会遇到此问题。 但是，考虑到 Azure 服务总线中的实体数，有时需要为服务总线客户的一小部分实施此缓解措施。

### <a name="service-bus-failure-on-a-single-subsystem"></a>单个子系统上的服务总线故障
使用任何应用程序时，环境都可能导致服务总线的内部组件出现不一致。 当服务总线检测到这种不一致时，它将从该应用程序收集数据以辅助诊断问题。 收集到数据后，将重新启动该应用程序以尝试使其返回一致状态。 此过程发生得相当迅速，并且会导致实体长达数分钟不可用，而典型的停机时间则要短得多。

在这些情况下，客户端应用程序将生成 [System.TimeoutException][System.TimeoutException] 或 [MessagingException][MessagingException] 异常。 服务总线通过自动客户端重试逻辑来缓解该问题。 如果重试周期用尽而未能传递消息，可以尝试使用其他功能，例如[成对命名空间][paired namespaces]。 成对命名空间具有其他一些注意事项，本文对此进行了讨论。

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Azure 数据中心内的服务总线故障
造成 Azure 数据中心内故障的最可能原因是，服务总线或依赖系统升级部署失败。 随着平台的成熟，出现此类型故障的可能性已降低。 数据中心出现故障的原因还可能包括以下方面：

* 电力中断（电源和生成的电力消失）。
* 连接性（客户端与 Azure 之间 Internet 断开）。

对于这两种情况，自然或人为灾难都可能导致此问题发生。 要解决此问题并确保仍可发送消息，可以使用[成对命名空间][paired namespaces]，以允许在主位置恢复正常时能够将消息发送到第二个位置。 有关详细信息，请参阅[使应用程序免受服务总线中断和灾难影响的最佳实践][Best practices for insulating applications against Service Bus outages and disasters]。

## <a name="paired-namespaces"></a>成对命名空间
[成对命名空间][paired namespaces]功能支持在数据中心内的服务总线实体或部署不可用时的应用场景。 虽然此故障很少发生，但仍必须准备分布式系统以应对最坏情况。 通常情况下，发生此故障是由于服务总线所依赖的某些元素发生短期问题。 为了维护应用程序在中断期间的可用性，服务总线用户可使用两个单独的命名空间（最好位于独立的数据中心内）来托管其消息实体。 此部分的剩余部分使用了下列术语：

* 主命名空间：应用程序与之进行交互以便进行发送和接收操作的命名空间。
* 辅助命名空间：充当主命名空间备份的命名空间。 应用程序逻辑不与此命名空间进行交互。
* 故障转移时间间隔：应用程序从主命名空间切换到辅助命名空间之前接受常见故障的时间长度。

成对命名空间支持*发送可用性*。 发送可用性保留发送消息的能力。 若要使用发送可用性，应用程序必须满足以下要求：

1. 仅从主命名空间接收消息。
2. 已发送到特定队列或主题的消息可能会无序到达。
3. 某个会话中的消息可以无序到达。 这突破了会话的正常功能。 这意味着应用程序可以使用会话对消息进行逻辑分组。
4. 仅在主命名空间上保持会话状态。
5. 主队列可以在辅助队列将所有消息都传送到主队列之前进入联机状态并开始接收消息。

以下各节介绍了 API 以及实现 API 的方式，并显示了使用此功能的示例代码。 请注意，此功能将对计费产生相关影响。

### <a name="the-messagingfactorypairnamespaceasync-api"></a>MessagingFactory.PairNamespaceAsync API
成对命名空间功能包含针对 [Microsoft.ServiceBus.Messaging.MessagingFactory][Microsoft.ServiceBus.Messaging.MessagingFactory] 类的 [PairNamespaceAsync][PairNamespaceAsync] 方法：

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

任务完成后，命名空间配对也随即完成并可以响应使用 [MessagingFactory][MessagingFactory] 实例创建的任何 [MessageReceiver][MessageReceiver]、[QueueClient][QueueClient] 或 [TopicClient][TopicClient]。 [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] 是各种配对类型的基类，可通过 [MessagingFactory][MessagingFactory] 对象使用它。 目前，唯一的派生类名为 [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions]，它可实现发送可用性要求。 [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] 具有一组相互依存的构造函数。 查看参数最多的构造函数，就能理解其他构造函数的行为。

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

这些参数具有以下含义：

* *secondaryNamespaceManager*：辅助命名空间的一个初始化 [NamespaceManager][NamespaceManager] 实例，[PairNamespaceAsync][PairNamespaceAsync] 方法可用它来设置辅助命名空间。 使用命名空间管理器来获取命名空间中队列的列表，并确保存在所需积压工作队列。 如果这些队列不存在，则会创建它们。 [NamespaceManager][NamespaceManager] 要求能够通过 **Manage** 声明创建令牌。
* *messagingFactory*：辅助命名空间的 [MessagingFactory][MessagingFactory] 实例。 [MessagingFactory][MessagingFactory] 对象用于发送消息；如果 [EnableSyphon][EnableSyphon] 属性设置为 **true**，它还能从积压工作队列接收消息。
* *backlogQueueCount*：要创建的积压工作队列数。 此值必须至少为 1。 向积压工作发送消息时，随机选择这些队列之一。 如果将值设置为 1，则只能使用一个队列。 当发生这种情况并且该积压工作队列生成错误时，客户端将无法尝试使用其他积压工作队列，因而可能无法发送消息。 我们建议将此值设置为更大数值，默认将其设置为 10。 可以根据应用程序每天发送的数据量，将它设置为更大或更小的值。 每个积压工作队列可容纳至多 5 GB 的消息。
* *failoverInterval*：将任何一个实体切换到辅助命名空间之前，主命名空间上将接受故障的时间长度。 在逐个实体的基础上发生故障转移。 单个命名空间中的实体常常位于服务总线中的不同节点内。 一个实体中存在故障不表示另一个实体中也存在故障。 可以将此值设置为 [System.TimeSpan.Zero][System.TimeSpan.Zero]，以便在第一次非暂时性故障之后立即向辅助命名空间进行故障转移。 任何 [IsTransient][IsTransient] 属性为 false 的 [MessagingException][MessagingException] 或 [System.TimeoutException][System.TimeoutException] 故障都将触发故障转移计时器。 其他异常（如 [UnauthorizedAccessException][UnauthorizedAccessException]）不会导致故障转移，因为它们指示客户端配置不正确。 [ServerBusyException][ServerBusyException] 不会引发故障转移，因为正确模式是等待 10 秒，再重新发送消息。
* *enableSyphon*：指示此特定配对应将辅助命名空间中的消息同步回主命名空间。 通常情况下，发送消息的应用程序应将此值设置为 **false**；接收消息的应用程序应将此值设置为 **true**。 这样做的原因是，消息接收方通常少于消息发送方。 根据接收方的数量，可以选择使用单个应用程序实例处理同步任务。 使用许多接收方将对每个积压工作队列的计费产生影响。

若要使用代码，请创建一个 [MessagingFactory][MessagingFactory] 主实例、一个 [MessagingFactory][MessagingFactory] 辅助实例、一个 [NamespaceManager][NamespaceManager] 辅助实例和一个 [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] 实例。 调用可以很简单，如下所示：

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

[PairNamespaceAsync][PairNamespaceAsync] 方法返回的任务完成后，所有内容都已设置完毕并且可供使用。 在该任务返回之前，可能尚未完成使所有配对正确工作所需的后台工作。 因此，应在任务返回后才开始发送消息。 如果出现任何故障（例如凭据错误或无法创建积压工作队列），则会在该任务完成后立即引发这些异常。 该任务返回后，请通过检查 [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] 实例的 [BacklogQueueCount][BacklogQueueCount] 属性来验证已找到或创建队列。 对于前面的代码，该操作会显示如下：

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>后续步骤
了解服务总线中的异步消息传送的基础知识后，可阅读有关[成对命名空间][paired namespaces]的详细信息。

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_EnableSyphon
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_BacklogQueueCount
[paired namespaces]: service-bus-paired-namespaces.md
