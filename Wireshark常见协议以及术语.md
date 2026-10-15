| **Info 内容**                 | **协议**       | **作用**            | **出现场景**              |
| ----------------------------- | -------------- | ------------------- | ------------------------- |
| **Neighbor Solicitation**     | ICMPv6 / NDP   | IPv6 通过 IP 找 MAC | 访问 IPv6 邻居、地址检测  |
| **Multicast Listener Report** | ICMPv6 / MLDv2 | 加入 IPv6 组播组    | 网卡启动、IPv6 自动维护   |
| **Router Solicitation**       | ICMPv6         | 寻找 IPv6 路由器    | 主机刚接入网络            |
| **DHCP Request**              | DHCP           | 获取 IPv4 配置      | 主机申请 IP               |
| **Who has…? Tell…?**          | ARP            | IPv4 通过 IP 找 MAC | 访问同一 LAN 设备         |
| **mDNS query/response**       | mDNS           | 局域网服务发现      | 查找打印机、`.local` 主机 |

# 1. Neighbor Solicitation

```text
Neighbor Solicitation for fe80::b839:47ff:fe20:d383
```

这是 **ICMPv6 Neighbor Discovery Protocol, NDP** 的一部分。

它的作用类似 IPv4 里的 **ARP**。也就是：

- 我知道某个 IPv6 地址，但我不知道它对应的 MAC 地址，所以我要问：“谁拥有这个 IPv6 地址？”

在 IPv4 中你会看到：

```text
Who has 130.166.120.70? Tell 130.166.120.228
```

在 IPv6 中对应的就是：

```text
Neighbor Solicitation for fe80::...
```

### **出现场景**

通常出现在：

- 设备刚接入网络
- 设备想和某个 IPv6 地址通信
- 设备检查自己的 IPv6 地址有没有冲突
- 系统自动发现局域网内的邻居设备

**Neighbor Solicitation = IPv6 版本的“你是谁？你的 MAC 是多少？”**



**Neighbor Solicitation（NS）** 一般对应的回应包叫：**Neighbor Advertisement（NA）**

- **NS = 谁有这个 IPv6 地址？**
- **NA = 这个 IPv6 地址是我，我的 MAC 是这个。**

在 Wireshark 里，可能会看到类似两行：

```text
ICMPv6  Neighbor Solicitation for fe80::xxxx
ICMPv6  Neighbor Advertisement fe80::xxxx
```

或者更完整一点：

```text
Source: fe80::aaaa
Destination: ff02::1:ffxx:xxxx
Info: Neighbor Solicitation for fe80::bbbb
```

回应包：

```text
Source: fe80::bbbb
Destination: fe80::aaaa
Info: Neighbor Advertisement fe80::bbbb
```

点开回应包后，一般展开：

```text
Internet Control Message Protocol v6
    Type: Neighbor Advertisement
    Target Address: fe80::bbbb
    ICMPv6 Option
        Target Link-layer Address: aa:bb:cc:dd:ee:ff
```

这里最重要的是：

```text
Target Link-layer Address: aa:bb:cc:dd:ee:ff
```

这就是目标主机的 **MAC address**。

有时候看不到回应？可能有几种情况：

1. **目标设备没有在线**，所以没有回应。
2. **回应包被过滤掉了**，比如你只看到了 NS，没显示 NA。
3. **目标地址是自己本机的地址**，这是在做 **Duplicate Address Detection, DAD**，不一定会有正常回应。
4. **抓包位置看不到回应**，但如果你是在 Apollo 自己抓包，Apollo 发出的 NS 和收到的 NA 通常都能看到。











------

## **2️⃣ Multicast Listener Report Message v2**

截图中很多：

```text
Multicast Listener Report Message v2
```

这是 **ICMPv6 / MLDv2** 的消息。

**MLD = Multicast Listener Discovery**

它的作用是告诉交换机/路由器：

我这台机器想加入某些 IPv6 multicast group。

比如你截图中有：

```text
Destination: ff02::16
```

`ff02::16` 是 IPv6 multicast 相关地址。

### **出现场景**

通常出现在：

- 网卡刚启用
- 设备刚获得 IPv6 地址
- 系统加入某些 multicast group
- IPv6 网络自动维护时

为什么需要它？

因为 IPv6 很依赖 **multicast**，不像 IPv4 那样大量使用 broadcast。

比如：

- 找路由器
- 找邻居
- mDNS 服务发现
- IPv6 地址冲突检测

都可能用到 multicast。

简单理解：

**Multicast Listener Report = 设备告诉网络：“我要监听某些组播消息。”**

------

## **3️⃣ Router Solicitation**

截图中：

```text
Router Solicitation from 10:98:36:af:ef:9a
Router Solicitation from 00:30:6e:5c:4d:bc
```

这是 **ICMPv6 Router Discovery** 的一部分。

意思是：

设备在问：“这个网络里有没有 router？请告诉我网关和 IPv6 配置信息。”

这里后面的：

```text
10:98:36:af:ef:9a
00:30:6e:5c:4d:bc
```

是发出这个请求的设备的 **MAC address**。

### **出现场景**

通常出现在：

- 设备刚加入网络
- 设备想自动配置 IPv6 地址
- 设备想知道默认网关是谁
- 设备想获取网络前缀，例如 `/64`

简单理解：

**Router Solicitation = 主机问：“网关在哪里？”**

对应地，路由器可能会回复：

```text
Router Advertisement
```

意思是：

“我是 router，你可以用这些 IPv6 配置信息。”

------

## **4️⃣ DHCP Request**

截图中：

```text
DHCP Request - Transaction ID 0xe9702823
```

这是 **DHCP** 流量，用于 IPv4 自动配置。

设备通过 DHCP 获取：

- IPv4 address
- subnet mask
- default gateway
- DNS server

### **出现场景**

一般是 Apollo 或其他主机刚加入网络时，会向 DHCP server 请求 IP 配置。

简单理解：

**DHCP Request = 主机向 DHCP 服务器申请或确认 IP 地址。**

------

## **5️⃣ ARP: Who has … Tell …**

截图中：

```text
Who has 130.166.120.243? Tell 130.166.120.228
Who has 130.166.120.70? Tell 130.166.120.228
```

这是 **ARP Request**。

意思是：

130.166.120.243 是谁？请告诉 130.166.120.228。
 130.166.120.70 是谁？请告诉 130.166.120.228。

这里：

```text
130.166.120.228
```

很可能就是 **Apollo 的 IPv4 address**。

因为抓包点在 Apollo，而且它正在询问其他主机的 MAC 地址。

### **出现场景**

当 Apollo 想访问同一个局域网内的设备，比如 Hera / Intranet server 时，它需要知道对方的 **MAC address**。

所以 Apollo 会先发 ARP：

```text
Who has Hera_IP? Tell Apollo_IP
```

对方会回复：

```text
Hera_IP is at Hera_MAC
```

简单理解：

**ARP = IPv4 里通过 IP 找 MAC。**

------

## **6️⃣ mDNS: Standard query / response**

截图中：

```text
Standard query PTR _ipps._tcp.local
Standard query PTR _ipp._tcp.local
Standard query response ... u20d-mate.local AAAA ...
```

这是 **mDNS = Multicast DNS**。

它用于局域网内服务发现，不需要传统 DNS server。

### **`_ipp._tcp.local`** **和** **`_ipps._tcp.local`**

这两个通常和打印服务有关：

- **IPP** = Internet Printing Protocol
- **IPPS** = secure IPP

所以这些包可能是在自动寻找局域网里的打印机服务。

### **`u20d-mate.local`**

这是一个本地域名，类似：

```text
u20d-mate.local
```

`.local` 通常是 mDNS 使用的本地名称。

### **出现场景**

通常出现在：

- 系统自动寻找局域网服务
- 查找打印机
- 查找 AirPrint
- 查找共享设备
- 解析 `.local` 主机名

简单理解：

**mDNS = 局域网里的自动服务发现。**