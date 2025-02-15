---
title: TiKV 配置参数
aliases: ['/docs-cn/dev/command-line-flags-for-tikv-configuration/','/docs-cn/dev/reference/configuration/tikv-server/configuration/']
---

# TiKV 配置参数

TiKV 的命令行参数支持一些可读性好的单位转换。

+ 文件大小（以 bytes 为单位）：KB, MB, GB, TB, PB（也可以全小写）
+ 时间（以毫秒为单位）：ms, s, m, h

## `-A, --addr`

+ TiKV 监听地址。
+ 默认："127.0.0.1:20160"
+ 如果部署一个集群，\-\-addr 必须指定当前主机的 IP 地址，例如 "192.168.100.113:20160"，如果是运行在 docker 则需要指定为 "0.0.0.0:20160"。

## `--advertise-addr`

+ TiKV 对外访问地址。
+ 默认：${addr}
+ 在某些情况下，比如 Docker 或者 NAT 网络环境，客户端并不能通过 `--addr` 的地址来访问到 TiKV。这时候，你可以设置 `--advertise-addr` 来让客户端访问 TiKV。
+ 例如，docker 内部 IP 地址为 172.17.0.1，而宿主机的 IP 地址为 192.168.100.113 并且设置了端口映射 -p 20160:20160，那么可以设置为 \-\-advertise-addr="192.168.100.113:20160"，客户端可以通过 192.168.100.113:20160 来找到这个服务。

## `--status-addr`

+ TiKV 服务状态监听端口。
+ 默认："20180"
+ Prometheus 统计可以通过 `http://host:status_port/metrics` 访问。
+ Profile 数据可以通过 `http://host:status_port/debug/pprof/profile` 访问。

## `--advertise-status-addr`

+ TiKV 对外访问服务状态地址。
+ 默认：使用 `--status-addr`
+ 在某些情况下，例如 docker 或者 NAT 网络环境，客户端并不能通过 `--status-addr` 的地址来访问到 TiKV。此时，你可以设置 `--advertise-status-addr` 来让客户端访问 TiKV。
+ 例如，Docker 内部 IP 地址为 `172.17.0.1`，而宿主机的 IP 地址为 `192.168.100.113` 并且设置了端口映射 `-p 20180:20180`，那么可以设置 `--advertise-status-addr="192.168.100.113:20180"`，客户端可以通过 `192.168.100.113:20180` 来找到这个服务。

## `-C, --config`

+ 配置文件。
+ 默认：""
+ 如果你指定了配置文件，TiKV 会首先读取配置文件的配置。然后如果对应的配置在命令行参数里面也存在，TiKV 就会使用命令行参数的配置来覆盖配置文件里面的。

## `--capacity`

+ TiKV 存储数据的容量。
+ 默认：0（无限）
+ PD 需要使用这个值来对整个集群做 balance 操作。（提示：你可以使用 10GB 来替代 10737418240，从而简化参数的传递）。

## `--config-info <FORMAT>`

+ 按照指定的 `FORMAT` 输出各个配置项的取值信息并退出。
+ `FORMAT` 可选值：`json`。
+ 目前仅支持以 JSON 格式输出每个配置项的名字 (Name)、默认值 (DefaultValue) 和当前配置值 (ValueInFile)。当执行此命令时，若同时指定了 `-C` 或 `--config` 参数，则对应的配置文件包含的配置项会同时输出当前配置值和默认值，其他未指定的配置项仅输出默认值，示例如下：

```json
{
  "Component": "TiKV Server",
  "Version": "6.2.0",
  "Parameters": [
    {
      "Name": "log-level",
      "DefaultValue": "info",
      "ValueInFile": "warn"
    },
    {
      "Name": "log-file",
      "DefaultValue": ""
    },
    ...
  ]
 }
```

## `--data-dir`

+ TiKV 数据存储路径。
+ 默认："/tmp/tikv/store"

## `-L`

+ Log 级别。
+ 默认："info"
+ 可选值："debug"，"info"，"warn"，"error"，"fatal"

## `--log-file`

+ Log 文件。
+ 默认：""
+ 如果没设置这个参数，log 会默认输出到 "stderr"，如果设置了，log 就会输出到对应的文件里面。

## `--pd`

+ PD 地址列表。
+ 默认：""
+ TiKV 必须使用这个值连接 PD，才能正常工作。使用逗号来分隔多个 PD 地址，例如：192.168.100.113:2379, 192.168.100.114:2379, 192.168.100.115:2379。
