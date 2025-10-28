[合集 - 可观测性(34)](https://github.com)

[1.可观测性三支柱？远不止此！2023-05-16](https://github.com/ulricqin/p/17401770.html)[2.开源的Datadog？可观测性平台SigNoz是否名副其实？2023-07-20](https://github.com/ulricqin/p/17568861.html)[3.使用 OpenTelemetry 构建可观测性 01 - 介绍2023-08-15](https://github.com/ulricqin/p/17631878.html)[4.使用 OpenTelemetry 构建可观测性 03 - 导出2023-08-17](https://github.com/ulricqin/p/17638704.html)[5.使用 OpenTelemetry 构建可观测性 02 - 埋点2023-08-16](https://github.com/ulricqin/p/17633920.html)[6.使用 OpenTelemetry 构建可观测性 04 - 收集器2023-08-21](https://github.com/ulricqin/p/17646569.html)[7.使用 OpenTelemetry 构建可观测性 05 - 传播和行李（Propagation & Baggage）2023-08-24](https://github.com/ulricqin/p/17653273.html)[8.使用 OpenTelemetry 构建可观测性 06 - 生态系统2023-08-24](https://github.com/ulricqin/p/17653332.html)[9.Grafana 开源了一款 eBPF 采集器 Beyla2023-09-27](https://github.com/ulricqin/p/17733177.html)[10.可观测性建设实践之 - 日志分析的权衡取舍2023-11-25](https://github.com/ulricqin/p/17855221.html)[11.ClickHouse + ClickVisual 构建日志平台2023-12-01](https://github.com/ulricqin/p/17867075.html)[12.理想的监控系统到底是什么样的？2023-12-18](https://github.com/ulricqin/p/17911103.html)[13.可观测性与传统监控的区别和联系2024-01-16](https://github.com/ulricqin/p/17967861)[14.你唯一需要的是“Wide Events”，而非“Metrics、Logs、Traces”2024-04-26](https://github.com/ulricqin/p/18159755)[15.16.教你一招，告警恢复时如何拿到恢复时的值？2024-06-12](https://github.com/ulricqin/p/18241603)[17.在 Kubernetes 中部署 Alertmanager2024-08-06](https://github.com/ulricqin/p/18345101)[18.又来一个挑战 Elastic 的，初识 SigLens04-21](https://github.com/ulricqin/p/18837909)[19.开源夜莺V8.Beta11发版，支持CK告警、事件Pipeline等06-04](https://github.com/ulricqin/p/18909483):[wgetcloud](https://changshuaijiao.org)[20.夜莺监控V8发版，内置支持 DeepSeek 对接06-24](https://github.com/ulricqin/p/18945550)[21.CPU 负载高，到底应不应该告警？07-23](https://github.com/ulricqin/p/19000685)[22.底层的告警，上层业务应该收吗？07-24](https://github.com/ulricqin/p/19002910)[23.如何监控多个进程的存活和CPU、内存占用08-08](https://github.com/ulricqin/p/19028268)[24.为 Prometheus 告警规则增加 UI 管理能力08-10](https://github.com/ulricqin/p/19030543)[25.Prometheus 告警时为何无法获取现场值08-12](https://github.com/ulricqin/p/19033259)[26.监控系统如何选型：Zabbix vs Prometheus08-13](https://github.com/ulricqin/p/19035739)[27.夜莺监控的几种架构模式详解08-14](https://github.com/ulricqin/p/19038328)[28.Prometheus 监控 Kubernetes Cluster 最新极简教程08-15](https://github.com/ulricqin/p/19039753)[29.可观测性体系建设五步心法：明业务、立规范、采数据、显特征、获洞见08-18](https://github.com/ulricqin/p/19044045)[30.Grafana侧重可视化，那多数据源告警呢？08-21](https://github.com/ulricqin/p/19050938)[31.利用 OpenTelemetry 建设尾部采样08-26](https://github.com/ulricqin/p/19059200)[32.夜莺监控设计思考（一）整体定位、架构设计、单进程多进程选择、高可用设计10-14](https://github.com/ulricqin/p/19140005)[33.利用 OpenTelemetry 集成 JMX 监控10-14](https://github.com/ulricqin/p/19141299)

34.夜莺监控设计思考（三）时序库、agent 的一些设计考量10-28

收起

这将是一个系列，讲解 [夜莺监控](https://github.com) 的设计思考，可以理解为原理+最佳实践+产品设计时的折中取舍。

本系列其他文章：

* [夜莺监控设计思考（一）项目定位、组件思考、单进程多进程选择、高可用设计](https://github.com)
* [夜莺监控设计思考（二）边缘架构的缘起和设计](https://github.com)

本篇主要回答：

* 夜莺和时序库对接的设计逻辑
* 夜莺和 agent 对接的设计逻辑

## 夜莺和时序库对接的设计逻辑

如果是夜莺老用户，应该知道在 V4 以及之前的版本，夜莺是有自研时序库的。而 V5 开始放弃了自研时序库，转而做各类数据源的对接，这其中是怎么一个考虑？

V4 之前的版本，其实是沿袭了很多 Open-Falcon 的设计逻辑，甚至想做一款运维平台。后来发现有如下问题：

* 运维平台是一个很大的东西，需要投入很多人力，靠开源社区比较难
* 运维平台是一个没有共识的产品，各家或多或少会有自己的额外需求，什么需求都想往里塞
* 观察国内外各个知名的开源项目，都是有清晰专注的定位，通常来讲，定位越精准细分，越容易做透
* 社区用户来用夜莺，大都是奔着其监控功能来的而非其他功能

本质就是**项目定位**的考虑。纵观整个监控领域，时序库、采集器、可视化工具，都有对应的知名开源项目了，唯独告警引擎，是缺失的，所以，夜莺从 V5 开始，就重点做告警引擎。

作为一款告警引擎，需要有自己的时序库么？显然是不需要的，去对接各类现成的时序库就可以了。所以从 V5 开始，夜莺不做自己的时序库了，整个架构示意图如下：

![image](https://img2024.cnblogs.com/blog/2036615/202510/2036615-20251028201943054-2140943001.png)

**但是**

这个架构是需要用户自己搞定采集器+存储的，拿场景最大的指标场景举例，需要用户自行搞定 Prometheus（或 VictoriaMetrics）+ 各类 Exporter。当然，除了 Exporter，社区里还有别的采集器。

我们希望让社区用户轻松一些，所以往前走了一步，虽然夜莺不做时序库，但可以接时序数据，然后转存到时序库。夜莺同时支持多种采集器，比如 Datadog-agent、Grafana-agent、Alloy、Telegraf 等，以及后来的 Categraf。

不同的采集器有不同的协议，夜莺支持多种指标写入协议，最终把数据转存给时序库。架构示意图如下：

![image]()

夜莺的配置文件 config.toml 里有 Pushgw 部分，就是用于配置后端时序库的地址，可以配置多个 Writer，数据就会同时转存到多个后端，当然，你也可以不配置 Writer，不用夜莺来转发数据。

> 监控数据采集领域，典型有两种模式，PULL和PUSH，Prometheus的方式就是PULL模式，Datadog、Categraf 的模式是PUSH模式。

夜莺提供了数据接收的 HTTP 端口，是典型的 PUSH 模式，如果你想用 PULL 模式，继续使用 Prometheus + Exporter 即可。

PUSH 模式下，较难感知源端是否挂了，即 Nodata 告警，那夜莺既然选择了 PUSH 模式，也就专门提供了 Nodata 告警能力，所以上图中各类 PUSH 类型的 agent，如果数据经由夜莺转发，则享有了 Nodata 告警能力，可以方便得知道哪个 agent 挂了。

### 小结

夜莺的典型用例场景，是用户自行搞定数据采集和存储，夜莺仅作为告警引擎，对接数据源，做告警判定。

如果你也想让数据流经夜莺，建议选择下文介绍的 Categraf 采集器。和夜莺的整合最为丝滑。

## 夜莺和 agent 对接的设计逻辑

如前文介绍，社区已经有很多采集器了，为啥还要再搞一个 Categraf 呢？

社区使用最多的采集器，大概是各类 Exporter，但是 Exporter 比较零散，体现为：

* 不同的 Exporter 设计逻辑不同，有的和监控目标之间是一对一关系，有的是一对多关系
* 不同的 Exporter 日志打印方式不同
* 不同的 Exporter 传参、配置文件格式各异
* 有些采集需求没有对应的 Exporter

所以，我们想做一些整合，搞一个大一统的采集器，同时，还有另一个重要原因：

夜莺除了开源版，还有企业版，企业用户需要一个一致的产品体验，给他一堆 Exporter 不太能接受，其次，企业用户各种稀奇古怪的采集需求，提到各个 Exporter 那响应太慢，另外，我们还想要采集规则下发能力。这些需求，势必需要一个单独的 agent，于是 Categraf 诞生了。

引入 Categraf 之后，架构示意图如下：

![image]()

夜莺有了自己的 agent 之后，就带来了额外能力：

* agent 可以采集一些机器的 metadata 信息上报给夜莺，让用户方便查看
* agent 和服务端周期性心跳，夜莺就可以额外生成一个机器列表，类似一个资产台账，当然，自己做 Table 仪表盘其实也可以

Categraf 的配置文件里，会配置两个夜莺接口地址，一个是 heartbeat 的，一个是 writer 的：

* heartbeat：心跳接口，用于告诉服务端自己活着，同时上报机器的 meta 信息，heartbeat 如果 Disable 了，夜莺的机器列表里就会看到机器的 CPU、内存等字段都是 Unknown，点击机器也看不到 metadata 信息
* writer：推送数据的接口，协议是 Prometheus remote write，其实 Categraf 也可以把 writer 直接配置为时序库的 remote write 地址，但是这样数据就不走夜莺了，不太推荐

### 不想用 Categraf 行不行

其实是可以的，夜莺本就侧重做告警引擎，你自己搞定数据采集和存储是完全 OK 的。

不过，如果是新项目，还是建议使用 Categraf，至少用 Categraf 采集机器的 CPU、内存、进程、metadata 等基础信息，这样体验更好。至于各类数据库、中间件的监控数据，可以不用 Categraf，用你自己习惯的采集器即可。

## 总结

在夜莺体系里，最简单的用法，就是仅使用 `n9e` 进程，作为告警引擎，如果有边缘机房的情况，可以继续引入 `n9e-edge` 做边缘机房的告警。

Categraf 也不是必须的，不过用 Categraf 采集机器的基础指标和夜莺打通，体验会更好。看你自己的选择吧。
