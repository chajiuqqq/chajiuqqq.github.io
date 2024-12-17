tc 允许你对系统的网络进行微调，包括模拟各种网络问题，例如延迟、丢包和带宽限制。netem (network emulator) 是 tc 的一个 qdisc (queueing discipline)，它允许你模拟各种网络条件，包括延迟、丢包和带宽限制。

```bash
// ping测试
[root@ej-hu ~]# ping 192.168.35.135
PING 192.168.35.135 (192.168.35.135) 56(84) bytes of data.
64 bytes from 192.168.35.135: icmp_seq=1 ttl=62 time=4.31 ms
64 bytes from 192.168.35.135: icmp_seq=2 ttl=62 time=4.11 ms
64 bytes from 192.168.35.135: icmp_seq=3 ttl=62 time=4.91 ms
64 bytes from 192.168.35.135: icmp_seq=4 ttl=62 time=4.28 ms

// 查看接口
[root@ej-hu ~]# ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens192: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether 00:50:56:a6:85:02 brd ff:ff:ff:ff:ff:ff
3: tailscale0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1280 qdisc pfifo_fast state UNKNOWN mode DEFAULT group default qlen 500
    link/none 

// 增加延迟
[root@ej-hu ~]# sudo tc qdisc add dev tailscale0 root netem delay 100ms

// 查询状态
[root@ej-hu ~]# tc qdisc show dev tailscale0
qdisc netem 8005: root refcnt 2 limit 1000 delay 100.0ms

// ping测试
[root@ej-hu ~]# ping 192.168.35.135
PING 192.168.35.135 (192.168.35.135) 56(84) bytes of data.
64 bytes from 192.168.35.135: icmp_seq=1 ttl=62 time=104 ms
64 bytes from 192.168.35.135: icmp_seq=2 ttl=62 time=105 ms
64 bytes from 192.168.35.135: icmp_seq=3 ttl=62 time=104 ms

// 删除配置
[root@ej-hu ~]# sudo tc qdisc del dev tailscale0 root

```
**`tc qdisc add dev ens192 root netem delay 100ms`解析**
该命令用于在 ens192 接口上模拟延迟，
解释每个部分：
sudo: 以超级用户权限执行命令。
tc: 流量控制命令。
qdisc: 排队规则。
add: 添加新的规则。
dev ens192: 指定目标接口为 ens192。
root: 在根级别添加新的排队规则。
netem: 使用网络模拟功能。
delay 100ms: 模拟 100 毫秒的网络延迟。

**使用 `tc` 设置的延迟在重启时不会保留。如果你希望它在每次系统启动时都能设置，可以考虑将上述命令添加到系统的启动脚本中（例如，在 `/etc/rc.local` 中）。**


---
## 为指定 IP 的请求增加延迟

要为指定 IP 的请求增加延迟，你可以在 Linux 系统中利用 `tc`（Traffic Control）工具实现。我们将使用 `iptables` 配合 `tc` 来针对特定的 IP 地址设置网络延迟。以下是实现的步骤：

### 步骤 1: 添加 `iptables` 规则

使用 `iptables` 来标记发送到特定 IP 地址的流量。假设你要为目标 IP 为 `192.168.1.100` 的流量设置延迟：

```bash
sudo iptables -t mangle -A OUTPUT -d 192.168.1.100 -j MARK --set-mark 1
```

这个命令将所有发往 `192.168.1.100` 的数据包标记为 `1`。

### 步骤 2: 创建 `tc` 队列规则

接下来，你需要使用 `tc` 来为标记的流量设置延迟。假设你要在 `ens192` 接口上添加 100 毫秒的延迟：

```bash
sudo tc qdisc add dev ens192 root handle 1: prio
sudo tc filter add dev ens192 protocol ip parent 1:0 prio 1 handle 1 fw classid 1:1
sudo tc qdisc add dev ens192 parent 1:1 handle 10: netem delay 100ms
```

### 解释这些命令：

1. **添加优先级队列**:
   - `sudo tc qdisc add dev ens192 root handle 1: prio`：为 `ens192` 接口添加一个根优先级队列。

2. **添加过滤器**:
   - `sudo tc filter add dev ens192 protocol ip parent 1:0 prio 1 handle 1 fw classid 1:1`：将根据 `iptables` 中标记的流量添加过滤器，这样所有标记为 `1` 的流量都会被处理。

3. **添加延迟**:
   - `sudo tc qdisc add dev ens192 parent 1:1 handle 10: netem delay 100ms`：在优先级队列中，为标记的流量增加 100 毫秒的延迟。

### 步骤 3: 验证配置

你可以运行以下命令检查你的设置：

```bash
tc -s qdisc show dev ens192
```

这将显示当前的排队规则以及相关的统计信息。

### 步骤 4: 移除延迟设置

如果你想要移除刚才设置的延迟，可以运行：

```bash
sudo tc qdisc del dev ens192 root
sudo iptables -t mangle -D OUTPUT -d 192.168.1.100 -j MARK --set-mark 1
```

### 注意事项

- 确保你已安装 `iproute2` 和 `iptables` 工具（通常这些在大多数 Linux 发行版中默认安装）。
- 请根据你的实际接口名称（如 `ens192`）替换命令中的接口名。
- 如果你的目标是 `INPUT` 流量（进入接口的流量），请将 `OUTPUT` 更改为 `INPUT` 并进行相应的调整。

有任何进一步问题或需要更详细的解释，请随时告诉我！


