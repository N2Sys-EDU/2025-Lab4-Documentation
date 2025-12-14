# Lab4

在 lab4 中，你将从零开始上手网络模拟器 ns-3 ，并使用 ns-3 进行一些简单的流量模拟并获取实验数据。

经过这个 lab ，你将对网络模拟器的作用有更深的理解。

## 一、ns-3 简介

lab4 的主要目的在于让大家了解 ns-3 的基本用法。因而，你的多数时间将会花在读文档上，而需要写的代码量会比前几个 lab 少很多。

### 网络模拟器简介

为什么我们需要网络模拟器？设想以下场景：某一天，你设计出了一种自认为绝妙的传输协议。你在你和你舍友的笔记本上部署了这个传输协议并进行了点对点的传输实验，发现在一些关键指标上取得了比 SOTA (State Of The Art) 提升 80\% 的性能。于是，你兴奋地把你的设计写成论文进行了投稿。然而，三个月后，你却发现自己的论文被拒稿了，审稿意见中写着：很精彩的想法，但是实验场景过于单一，缺少更大规模的实验验证，并且也难以复现。你很郁闷：我手上怎么也只能凑出两台设备，你让我搭建更丰富的实验场景，那买设备的钱你能帮我出吗？部署的时间你能帮我花吗？

但幸运的是，你突然想起来在本科上计算机网络时学习过使用网络模拟器。你善用搜索，发现近年来发表的会议论文中也有大量的文章在实验中用模拟器实验丰富了自己的实验设定。于是，你在模拟器中实现了你设计的协议并新增了一大堆实验，并重新投稿一鸣惊人拿下了 Best Paper 走上人生巅峰。

回归正题。随着网络技术的迅速发展，互联网规模和复杂性不断提高，网络新技术的研究与部署变得愈发困难。网络模拟器作为一种强大的研究工具，能够有效地帮助开发者、研究人员和工程师设计、分析和评估各种网络协议与系统性能，成为网络研究和教学中不可或缺的一部分。

网络模拟器主要解决了网络研究中的以下关键问题：

- 低成本试验环境：在现实中搭建大规模的网络环境需要大量硬件资源和资金成本，而使用模拟器可以以较低的成本创建一个虚拟的、功能完善的网络环境，模拟出真实网络中的通信行为和问题。
- 灵活的实验条件：模拟器提供了高度灵活的实验条件，用户可以自定义网络拓扑、通信协议、流量模型、节点行为等参数，便于测试新方案在各种场景下的性能。
- 安全性和可控性：在真实网络中测试新协议或技术可能会引发不可预知的问题，而模拟器能够在安全、可控的环境中运行实验，避免对现实网络造成影响。
- 可重复性和可比性：通过网络模拟器，可以对同一实验进行多次运行，获取一致的结果，便于验证实验的准确性，并与其他研究进行对比。

现存的网络模拟器有很多，各有各的特点。其中，使用十分广泛的一类就是*基于离散事件的网络模拟器*。我们在本 lab 中将要使用的 ns-3 一个知名的离散事件模拟器。在这类模拟器中，每个网络事件（如数据包的发送、接收、丢失等）会被分配一个时间戳，模拟器按照时间戳的先后顺序逐一处理事件，从而描绘出网络系统的动态运行过程。

### 什么是 ns-3 ？

ns-3（Network Simulator 3）是一款开源的离散事件网络模拟器，专为研究和开发网络协议而设计。与其前身 ns-2 相比，ns-3 提供了更现代化和模块化的架构，支持更复杂的网络模型和仿真场景。ns-3 可以用于模拟各种网络协议、网络拓扑以及应用程序，广泛应用于学术研究、工程开发以及教育领域。

本 lab 的文档会介绍所有完成本 lab 需要的 ns-3 知识。如果你对本 lab 的内容感到不满足的话，可以继续学习 [ns-3 的官方教程](https://www.nsnam.org/docs/tutorial/html/) 。

### ns-3 的原理简介

ns-3 基于离散事件模拟原理，允许用户在指定的时间点模拟网络事件的发生。ns-3 的核心是一个事件调度器，能够按照时间顺序处理事件， 例如数据包的发送、接收和丢弃。ns-3 的架构高度模块化，用户可以通过 C++ 或 Python 编写仿真脚本，并可以自由组合和扩展现有的网络协议和组件。

ns-3 还提供了丰富的仿真模型，包括无线和有线网络、移动网络、传输协议等。此外，ns-3 支持与其他工具（如 Wireshark）集成，以便进行更深入的分析和可视化。

本 lab 中，我们只会使用到它的仿真模型的一个子集，不过这足以让你对 ns-3 的使用方式有一个比较全面的了解。

### 如何编译 ns-3.38 并测试编译的正确性

在 lab4 中，我们将会使用 3.38 版本的 ns-3 。

如果你使用的是本课程提供的镜像的话，在镜像的 `/home/student/workspace/ns-allinone-3.38/ns-3.38` 已经包含了在 x86 平台上 预编译好的 ns-3 ，你可以跳过下载、编译的步骤，直接跳转到 “3. 测试编译的正确性” 一步。

1. 下载源代码并解压。

   ```bash
   wget https://www.nsnam.org/releases/ns-allinone-3.38.tar.bz2
   tar -xjvf ns-allinone-3.38.tar.bz2
   cd ns-allinone-3.38/ns-3.38
   ```
2. 配置并编译 ns-3 。

   这一步有可能会需要比较长的时间。为了避免编译到一半你的机器发生断网（如果你在使用远程服务器）之类的突发情况，建议开一个 tmux 窗口并在其中进行编译。

   ```bash
   ./ns3 configure --enable-examples --enable-tests
   ./ns3 build -j $(nproc)
   ```
3. 测试编译的正确性。

   编译完成后，可以运行示例以验证编译成功。可以运行以下命令测试 ns-3 的实例：

   ```bash
   ./ns3 run hello-simulator
   ```

   如果一切顺利，你应该能在终端中看到模拟器的输出 `Hello Simulator` 。

### lab 任务介绍

在本 lab 中，你将完成以下的任务：理解基于哑铃型拓扑的样例程序，并学习如何在 ns-3 中进行模拟实验并获取实验数据。

## 二、lab 任务

本 lab 中，我们将在 ns-3 中构建一个哑铃型拓扑，在该拓扑上进行简单的实验，并且使用 ns-3 提供的功能来获取实验数据。

在你通过 github classroom 获得的 starter code 中，可以找到在 ns-3 中构建哑铃型拓扑并进行模拟实验的样例程序 `dumbbell-base.cc` 。我们将先带你理解 `dumbbell-base.cc` 的代码，然后再教你如何使用 ns-3 提供的功能来获取你想要的实验数据。

本 lab 中你的工作目录应该始终为 `ns-3.38/` 目录，文档中使用的相对路径均为相对 `ns-3.38` 目录的路径。

本 lab 的部分任务和 `dumbbell-base.cc` 的代码参考了 `ns-3.38` 目录下的 `examples/tcp/tcp-linux-reno.cc` 示例文件。在完成代码任务时，你可以参考 `examples/tcp/tcp-linux-reno.cc` 中的实现方法，这会给你带来很大的帮助。

### Exercise 1 解读 Codebase

在本练习中，我们将带你理解样例程序 `dumbbell-base.cc`，你不需要写任何代码。

#### 1. 拓扑构建

在 `dumbbell-base.cc` 中，我们手动构建了一个哑铃型拓扑，如下图。

```
// - Network topology
//   - The dumbbell topology consists of 
//     - 4 servers (S0, S1, R0, R1)
//     - 2 routers (T0, T1) 
//   - The topology is as follows:
//
//                    S0                         R0
//     10 Mbps, 1 ms   |      1 Mbps, 10 ms       |   10 Mbps, 1 ms
//                    T0 ----------------------- T1
//     10 Mbps, 1 ms   |                          |   10 Mbps, 1 ms
//                    S1                         R1
//
```

##### Step1：创建结点

ns-3 中不区分主机和交换机、路由器的设备。这些设备统一抽象为结点（Node）。

创建一个网络，首先要声明网络中有哪些结点。在创建结点时，我们先定义一个 `NodeContainer` 类型的结点容器，然后使用结点容器的 `Create` 方法创造结点，参数为要创造的结点数量。之后，我们可以通过结点容器访问到其所创造的结点。

在 `dumbbell-base.cc` 中，我们使用如下的代码创造哑铃型拓扑中的所有节点。其中，`routers` 中存储了两个路由器结点 `T0` 和 `T1`，`leftNodes` 中存储了左侧的两个主机结点 `S0` 和 `S1`，`rightNodes` 中存储了右侧的两个主机结点 `R0` 和 `R1`。

```C++
// Create nodes
NodeContainer leftNodes;
NodeContainer rightNodes;
NodeContainer routers;
routers.Create(2);
leftNodes.Create(N1); // N1=2
rightNodes.Create(N2); // N2=2
```

##### Step2：创建链路

我们通常把网络中数据流流过的媒介称为信道（Channel）。当你用一根网线连接一台机器和路由器时，就是通过信道将这台机器与以太网连接。

在 ns-3 的模拟环境中，数据交换的媒介也被抽象为信道，你可以把结点连接到代表数据交换信道的对象上。

常用的信道模型有 `CsmaChannel`、`PointToPointChannel`、`WifiChannel`，本 lab 中我们使用 `PointToPointChannel` 用来模拟连接两个结点之间的链路。

在 `dumbbell-base.cc` 中，我们使用如下的代码设置链路的带宽 `DataRate` 和延迟 `Delay`，并在结点之间创建链路。

具体而言，在创建链路时，我们先定义一个 `PointToPointHelper` 的实例，使用其 `SetDeviceAttribute` 方法指定链路带宽，再使用 `SetChannelAttribute` 方法指定链路时延，最后使用 `Install` 方法在两个结点之间创建链路。

`Install` 方法接受两个参数，代表链路两端的两个结点，并且返回一个 `NetDeviceContainer` 对象，它包含了安装在链路两端的结点上的网络设备（NetDevice） 的指针集合。在 ns-3 中，网络设备这一抽象表示结点上的网络设备接口。主机端的每张网卡和交换机、路由器的每一个网口都被抽象为一个网络设备。

在 ns-3 中，这种“先在 `*Helper` 中指定对象属性，然后再用 `*Helper.Install` 方法创建对象实例”的模式是很常见的。我们将在之后多次看到类似的模式。

```C++
// Create the point-to-point link helpers and connect two router nodes
PointToPointHelper pointToPointRouter;
pointToPointRouter.SetDeviceAttribute("DataRate", StringValue("1Mbps"));
pointToPointRouter.SetChannelAttribute("Delay", StringValue("10ms"));

NetDeviceContainer routerToRouter = pointToPointRouter.Install(routers.Get(0), routers.Get(1));

// Create the point-to-point link helpers and connect leaf nodes to router
PointToPointHelper pointToPointLeaf;
pointToPointLeaf.SetDeviceAttribute("DataRate", StringValue("10Mbps"));
pointToPointLeaf.SetChannelAttribute("Delay", StringValue("1ms"));

std::vector<NetDeviceContainer> leftToRouter;
std::vector<NetDeviceContainer> routerToRight;
for (uint32_t i = 0; i < N1; i++)
{
    leftToRouter.push_back(pointToPointLeaf.Install(leftNodes.Get(i), routers.Get(0)));
}
for (uint32_t i = 0; i < N2; i++)
{
    routerToRight.push_back(pointToPointLeaf.Install(routers.Get(1), rightNodes.Get(i)));
}
```

##### Step3：设置协议栈和结点IP

设置协议栈时，只需定义一个 `InternetStackHelper`，然后使用 `Install` 方法将协议栈与所有结点绑定即可。

设置 IP 地址时，先设置定义一个 `Ipv4AddressHelper` 实例并设置好基 IP 地址和子网掩码，后续 IP 地址将根据这两个参数递增分配。

将 `Ipv4AddressHelper` 的 `Assign` 方法作用于之前获得的 `NetDeviceContainer` 实例，就能为其中的所有网络设备分配一个 IP 地址。

注意，每使用 `Assign` 方法进行一次 IP 地址分配之后，都要调用一次 `NewNetwork` 方法切换到另一个子网，后续在另一个子网中进行 IP 地址分配。这样可以避免发生 IP 地址冲突的情况。例如在本例子中，第一次调用 `Assign` 时，会按照设置在 `10.0.0.0/24` 这个子网内分配 IP 地址。在调用一次 `NewNetwork` 之后，下一次的 IP 地址分配就会在 `10.0.1.0/24` 这个子网中进行。

```C++
// Install internet stack on all the nodes
InternetStackHelper internetStack;

internetStack.Install(leftNodes);
internetStack.Install(rightNodes);
internetStack.Install(routers);

// Assign IP addresses to all the network devices
Ipv4AddressHelper ipAddresses("10.0.0.0", "255.255.255.0");

Ipv4InterfaceContainer routersIpAddress = ipAddresses.Assign(routerToRouter);
ipAddresses.NewNetwork();

std::vector<Ipv4InterfaceContainer> leftToRouterIPAddress;
for (uint32_t i = 0; i < N1; i++)
{
    leftToRouterIPAddress.push_back(ipAddresses.Assign(leftToRouter[i]));
    ipAddresses.NewNetwork();
}

std::vector<Ipv4InterfaceContainer> routerToRightIPAddress;
for (uint32_t i = 0; i < N2; i++)
{
    routerToRightIPAddress.push_back(ipAddresses.Assign(routerToRight[i]));
    ipAddresses.NewNetwork();
}

Ipv4GlobalRoutingHelper::PopulateRoutingTables();
```

#### 2. 设置拥塞控制算法

ns-3 中内置实现了常见的 TCP 拥塞控制算法，如 `Reno`、`Cubic`、`Dctcp`、`BBR` 等。如果需要，你也可以在网上找到各种 RDMA 网络的拥塞控制算法在 ns-3 中的开源实现。

在 `dumbbell-base.cc` 中，我们使用的是最简单的 `Reno` 算法。相关代码如下。

```C++
std::string tcpTypeId = "ns3::TcpLinuxReno";    // TCP variant to use

Config::SetDefault("ns3::TcpL4Protocol::SocketType",
                    TypeIdValue(TypeId::LookupByName(tcpTypeId)));
```

在这里，我们使用了 `Config::SetDefault` 来指定使用的拥塞控制算法。在 ns-3 中，`Config::SetDefault` 是一种全局配置机制，用于在程序运行时设置某些属性的默认值。它允许用户在创建对象之前，预先设置特定对象的属性默认值，从而影响所有后续创建的对象。

本例中的代码设置了 TCP 协议栈中所有新创建的 TCP Socket 的默认类型为 `ns3::TcpLinuxReno`，所有后续创建的 TCP socket 都会使用 Reno 拥塞控制算法处理数据包发送和拥塞控制。

在 `dumbbell-base.cc` 中，你可以看到多处 `Config::SetDefault` 的使用。我们在此不会详细解释每一句的含义。如果你想知道这些语句设置的属性都是什么意思，可以阅读 ns-3 源码或者查询 [ns-3 文档中的 Attribute List](https://www.nsnam.org/docs/release/3.38/doxygen/d3/d79/_attribute_list.html)。

#### 3. 设置队列

ns-3 中的队列类比 Linux 中的 `qdisc` 实现，实际上为网络设备处的发送队列。队列的相关代码可以在 `src/traffic-control` 下找到。

以下的代码将路由器 `T0` 的 `0` 号网络设备（即 `T0` 用于与 `T1` 相连的网口）处的发送队列设置为 FIFO 队列，并设置该队列的最大长度为 100 个报文。

安装队列时，需要先用 `Uninstall` 方法卸载默认的原队列，然后再安装。

```C++
std::string qdiscTypeId = "ns3::FifoQueueDisc";    // Queue disc for gateway

// Set default parameters for queue discipline
Config::SetDefault(qdiscTypeId + "::MaxSize", QueueSizeValue(QueueSize("100p")));

// Install queue discipline on router
TrafficControlHelper tch;
tch.SetRootQueueDisc(qdiscTypeId);
QueueDiscContainer qd;
tch.Uninstall(routers.Get(0)->GetDevice(0));
qd.Add(tch.Install(routers.Get(0)->GetDevice(0)).Get(0));
```

#### 4. 创建发包应用

ns-3 中实现了一系列不同类型的发包应用。如果需要，你也可以实现自己的发包应用。

这里介绍两个常用的发包应用：

- `BulkSendApplication`：尽可能快地发送报文。（实现在 `src/applications/model/bulk-send-application.{h,cc}`）
- `OnOffApplication`：周期性发送报文。（实现在 `src/applications/model/onoff-application.{h,cc}`）

我们在 `dumbbell-base.cc` 中使用的就是 `BulkSendApplication`。我们通过安装发包应用的方法，在哑铃型拓扑上创建了两条流，其中 `flow 0` 从 `S0` 发送到 `R0`， `flow 1` 从 `S1` 发送到 `R1`。

具体来说，我们需要先在流的接收结点处的某个端口上创建用于收包的 `PacketSink`，然后在流的发送节点上创建发包应用，并将目的地设置为相应的接收节点的 socket 地址。最后使用 `Start` 和 `Stop` 方法来指定应用的起始时间。

```C++
Time startTime = Seconds(10.0);    // Start time for the simulation
Time stopTime = Seconds(60.0);    // Stop time for the simulation

// Function to install BulkSend application
void
InstallBulkSend(Ptr<Node> node,  // 把应用装在哪个节点上
                Ipv4Address address,  // 接收方的 IP 地址
                uint16_t port,  // 接收方的端口
                std::string socketFactory)
{
    BulkSendHelper source(socketFactory, InetSocketAddress(address, port));
    source.SetAttribute("MaxBytes", UintegerValue(0));  // "0" 代表无数量限制地发包
    ApplicationContainer sourceApps = source.Install(node);
    sourceApps.Start(startTime);
    sourceApps.Stop(stopTime);
}

// Function to install sink application
void
InstallPacketSink(Ptr<Node> node, uint16_t port, std::string socketFactory)
{
    PacketSinkHelper sink(socketFactory, InetSocketAddress(Ipv4Address::GetAny(), port));
    ApplicationContainer sinkApps = sink.Install(node);
    sinkApps.Start(startTime);
    sinkApps.Stop(stopTime);
}

int
main(int argc, char* argv[])
{
    // Install packet sink at receiver side
    for (uint32_t i = 0; i < N2; i++)
    {
        uint16_t port = 50000 + i;
        // 在 R_i 上创建 PacketSink
        InstallPacketSink(rightNodes.Get(i), port, "ns3::TcpSocketFactory");
    }

    for (uint32_t i = 0; i < N1; i++)
    {
        uint16_t port = 50000 + i;
        // 在 S_i 上创建发包应用，从而创建从 S_i 到 R_i 的流
        InstallBulkSend(leftNodes.Get(i),
                        routerToRightIPAddress[i].GetAddress(1),
                        port,
                        socketFactory);
    }
}
```

#### 5. 开始模拟

我们已经做好了一切的准备工作，接下来就可以开始模拟了！

在下面的代码中，我们先用 `Simulator::Stop()` 设置了模拟的停止时间，然后调用 `Simulator::Run()` 开始模拟。开始模拟后，模拟器会按照时间顺序一个个处理系统中的所有事件，直到系统中没有任何事件或者时间已经到达设定的停止时间，模拟才会停止。最后，我们使用 `Simulator::Destroy()` 来结束模拟并回收资源。

```C++
// Set the stop time of the simulation
Simulator::Stop(stopTime);

// Start the simulation
Simulator::Run();

// Cleanup and close the simulation
Simulator::Destroy();
```

#### 6. 运行程序

首先，把 `dumbbell-base.cc` 文件放到 `scratch/` 目录下。

```bash
student@327fb651b54a:~/workspace/ns-allinone-3.38/ns-3.38$ ls scratch/
CMakeLists.txt  dumbbell-base.cc  nested-subdir  scratch-simulator.cc  subdir
```

然后，在 `ns3-3.38` 文件夹下，运行命令 `./ns3 run dumbbell-base`。该命令会先对程序进行编译（如果之前未编译或源码有改动），然后运行编译出的可执行文件。注意，这条命令中不需要加上源码文件的后缀 `.cc`。

如果一切顺利，你将会看到终端中类似下面的输出。

```bash
student@327fb651b54a:~/workspace/ns-allinone-3.38/ns-3.38$ ./ns3 run dumbbell-base
// 省略了很多中间输出...
-- Configuring done
-- Generating done
-- Build files have been written to: /home/student/workspace/ns-allinone-3.38/ns-3.38/cmake-cache
[0/2] Re-checking globbed directories...
[2/2] Linking CXX executable ../build/scratch/ns3.38-dumbbell-base-default
```

> 如果你使用的是我们提供的已经预编译了 ns-3 的镜像，或者如果你已经自己编译了 ns-3，那么这条命令只会增量式地编译 `dumbbell-base` 这一个模块，所以执行的速度会很快。

因为我们在 `dumbbell-base.cc` 中只进行了模拟实验，没有加入任何用于获取实验数据的代码，所以你确实找不到任何实验结果的输出，这是正常的。我们会在后面教会你如何获取实验数据。

现在，请你把 `dumbbell-base.cc` 复制一份到同一目录下，并将其命名为 `dumbbell.cc` 。之后的练习中你需要修改 `dumbbell.cc` 。

> 来自助教的问候：看完 Exercise 1，你可能会觉得已经读太多东西读累了。但好消息是，这已经占了整个文档阅读量的相当一部分了！

### Exercise 2 支持从命令行传入流的大小

在本练习中，我们将学习如何让 ns-3 程序接受从命令行传入的流大小参数。你将在 `dumbbell.cc` 中加入接受命令行参数并设置流大小的代码，代码量在10行以内。

做实验，经常需要~~调参~~把一份代码在不同的参数场景下运行。为了避免把参数数值直接硬编码在源码中导致每次修改都需要重新编译，如何通过命令行传参就成为了一个必备的技能。幸运地是，ns-3 中提供了一套接口，让你可以非常方便地通过命令行给程序传参。

如果你定义了一个名为 `MyVar` 的变量，并且想要支持从命令行传入它的值。那么你可以加上下面的几行代码。加上之后，你就可以通过形如 `./ns3 run "dumbbell-base --MyAar=42"` 的语句来传入命令行参数并运行程序。你可以在 `examples/tcp-linux-reno.cc` 中找到更多关于接受命令行参数的例子。事实上，ns-3 中的命令行参数接口其实有更强大的功能，还能用于直接覆盖默认的属性值，不过这一点在本 lab 中没有用到，有兴趣的同学可以翻阅 ns-3 的文档学习。

```C++
int MyVar;

CommandLine cmd;
cmd.AddValue("MyVar", "Description of this argument", MyVar);
cmd.Parse (argc, argv);
```

本练习中，我们需要让 `dumbbell.cc` 能够接受两个命令行参数，分别代表两条流 `flow 0` 和 `flow 1` 的大小（单位为 Byte）。你需要对代码做以下的修改：

1. 接受两个命令行参数 `flowSize0` 和 `flowSize1` ，分别代表 `flow 0` 和 `flow 1` 的大小。你可以用 `uint32_t` 来存放这两个大小值。
2. 修改 `InstallBulkSend()` 函数的参数列表和实现，使其可以多接受一个参数 `uint32_t flowSize`，并使用 `source.SetAttribute("MaxBytes",UintegerValue(flowSize));` 来通过限制发包应用的最大发送字节数来指定流的大小。当然，你也会需要修改调用 `InstallBulkSend()` 函数的地方，把从命令行接收到的流大小参数传进去。

> 来自助教的问候：你可能会觉得这个 Exercise 有点太简单了，这对吗？这对的！因为修改 ns-3 的代码确实就不难

修改完毕后，你就可以在命令行中试着运行 `./ns3 run "dumbbell --flowSize0=4000 --flowSize1=5000"` 。如果程序顺利地编译运行，没有显示任何报错，那么大概率你的实现就是正确的。你依然找不到任何实验结果的输出，这是符合预期的，所以你确实没有办法知道你的代码是否完全正确。不过很快你就能在 Exercise 3 中检验你在 Exercise 2 中的实现是否正确了。

### Exercise 3 获取 PCAP Trace

在本练习中，我们将学习如何获得 ns-3 模拟过程中的 PCAP Trace。你将在 `dumbbell.cc` 中加入用于获取 PCAP Trace 的代码，代码量在 10 行左右。

PCAP 是 **Packet Capture** 的缩写，它是一种网络数据包捕获文件格式，用于存储通过网络接口捕获的原始数据包，通常由 tcpdump 等工具生成，用于记录网络通信的详细信息，便于后续分析和调试。

ns-3 支持通过启用 **PCAP 捕获** 来生成 PCAP Trace 文件。ns-3 的 PCAP 捕获功能允许用户在仿真中捕获某些网络设备（NetDevice）的数据包，并将捕获结果保存为标准的 PCAP 文件格式。这些文件可以用 Wireshark 或 tcpdump 等工具进行分析。

下面是在 ns-3 中使用 `EnablePcap` 方法对某个网络设备启用 PCAP 捕获的例子。`EnablePcap` 方法有三个参数，为：

* `"point-to-point"` 是生成的 PCAP 文件的前缀（下面的例子中，输出的文件名将为 `point-to-point-0-0.pcap`）
* `devices.Get(0)` 是启用捕获的网络设备
* `true` 表示使用 promiscuous mode（混杂模式），这样可以捕获所有流经设备的数据包；如果设置为 `false` ，代表仅捕获发往该设备的数据包

```C++
PointToPointHelper p2p;
NetDeviceContainer devices = p2p.Install(nodes);

// 对一个网络设备启用 PCAP 捕获
p2p.EnablePcap("point-to-point", devices.Get(0), true);
```

除此之外，也可以使用 `EnablePcapAll` 方法对所有使用该 `PointToPointHelper` 实例创建出的网络设备启用 PCAP 捕获。

```C++
PointToPointHelper p2p;
NetDeviceContainer devices = p2p.Install(nodes);

// 启用使用 p2p 创建的所有网络设备的 PCAP 捕获
p2p.EnablePcapAll("point-to-point", true);
```

启用 PCAP 捕获后，ns-3 将自动把所有捕获的包信息输出到通过第一个参数（前缀）指定的文件位置。

本练习中，我们需要让 `dumbbell.cc` 能够输出【 `T0` 路由器上的所有网络设备（一共三个）的 PCAP Trace】。你需要对代码做以下的修改：

1. 使用 `PointToPointHelper` 的 `EnablePcap` 方法，对 `T0` 路由器的三个网络设备启用 PCAP 捕获，并且指定将输出的 `*.pcap` 文件放在 `lv1-results/pcap/` 目录下，并且使文件名的前缀为 "lv1"。需要使用 promiscuous mode（混杂模式）。你可以仿照   `examples/tcp-linux-reno.cc` 的方法来创建输出文件所在的目录。

修改完毕后，你可以在命令行中试着运行 `./ns3 run "dumbbell --flowSize0=1000 --flowSize1=2000"` 。如果一切正常，你应该得到如下的输出文件。（这里三个文件的命名方式是 `{prefix}-{#node}-{#device_of_node}`，其中 `prefix` 是你自己在 `EnablePcap` 中指定的，`#node` 是结点的全局编号（这里因为 `T0` 是首个被创建出的结点，所以其全局编号是 `0` ），`#device_of_node` 是对应的网络设备在结点上的编号（按照被创建的顺序编号，`T0T1` 链路上的设备编号是 `0` ，`S0T0` 链路上的设备编号是 `1` ，`S1T0` 链路上的设备编号是 `2` 。

```bash
student@327fb651b54a:~/workspace/ns-allinone-3.38/ns-3.38$ ls lv1-results/pcap/
lv1-0-0.pcap  lv1-0-1.pcap  lv1-0-2.pcap
```

我们可以使用 tcpdump 或 Wireshark 来解读 ` *.pcap` 文件。这里我们以使用  `tcpdump -r `来解析 `*.pcap `文件为例。下面展示了 `flowSize0=4000 `且 `flowSize1=5000 `时，在 `T0 `的 `0` 号网络设备上捕获到的包信息。因为开启了混杂模式，所以所有经过该设备的数据包都被捕获了。你可以在其中清晰地看到两条流中的每个包（包括 SYN 和 FIN）的记录。

> 如果你使用的是课程提供的 docker 镜像，那么你会发现容器中没有 `tcpdump` 可用，这是助教的失误导致的（构建镜像时忘了安装 `tcpdump`）。你可以在容器中运行 `sudo apt install tcpdump` 并输入密码 `123456` ，就可以安装 `tcpdump` 。

```bash
student@327fb651b54a:~/workspace/ns-allinone-3.38/ns-3.38$ tcpdump -r lv1-results/pcap/lv1-0-0.pcap 
reading from file lv1-results/pcap/lv1-0-0.pcap, link-type PPP (PPP), snapshot length 65535
00:00:10.001046 IP 10.0.1.1.49153 > 10.0.3.2.50000: Flags [S], seq 0, win 65535, options [TS val 10000 ecr 0,wscale 5,sackOK,eol], length 0
00:00:10.001510 IP 10.0.2.1.49153 > 10.0.4.2.50001: Flags [S], seq 0, win 65535, options [TS val 10000 ecr 0,wscale 5,sackOK,eol], length 0
00:00:10.024067 IP 10.0.3.2.50000 > 10.0.1.1.49153: Flags [S.], seq 0, ack 1, win 65535, options [TS val 10012 ecr 10000,wscale 5,sackOK,eol], length 0
00:00:10.024531 IP 10.0.4.2.50001 > 10.0.2.1.49153: Flags [S.], seq 0, ack 1, win 65535, options [TS val 10013 ecr 10000,wscale 5,sackOK,eol], length 0
00:00:10.026156 IP 10.0.1.1.49153 > 10.0.3.2.50000: Flags [.], ack 1, win 32768, options [TS val 10025 ecr 10012,eol], length 0
00:00:10.026620 IP 10.0.2.1.49153 > 10.0.4.2.50001: Flags [.], ack 1, win 32768, options [TS val 10025 ecr 10013,eol], length 0
00:00:10.027358 IP 10.0.1.1.49153 > 10.0.3.2.50000: Flags [.], seq 1:1449, ack 1, win 32768, options [TS val 10025 ecr 10012,eol], length 1448
00:00:10.039374 IP 10.0.2.1.49153 > 10.0.4.2.50001: Flags [.], seq 1:1449, ack 1, win 32768, options [TS val 10025 ecr 10013,eol], length 1448
00:00:10.051390 IP 10.0.1.1.49153 > 10.0.3.2.50000: Flags [.], seq 1449:2897, ack 1, win 32768, options [TS val 10025 ecr 10012,eol], length 1448
00:00:10.063051 IP 10.0.3.2.50000 > 10.0.1.1.49153: Flags [.], ack 1449, win 32768, options [TS val 10051 ecr 10025,eol], length 0
00:00:10.063406 IP 10.0.2.1.49153 > 10.0.4.2.50001: Flags [.], seq 1449:2897, ack 1, win 32768, options [TS val 10025 ecr 10013,eol], length 1448
00:00:10.075067 IP 10.0.4.2.50001 > 10.0.2.1.49153: Flags [.], ack 1449, win 32768, options [TS val 10063 ecr 10025,eol], length 0
00:00:10.075422 IP 10.0.1.1.49153 > 10.0.3.2.50000: Flags [F.], seq 2897:4001, ack 1, win 32768, options [TS val 10025 ecr 10012,eol], length 1104
00:00:10.084686 IP 10.0.2.1.49153 > 10.0.4.2.50001: Flags [.], seq 2897:4345, ack 1, win 32768, options [TS val 10025 ecr 10013,eol], length 1448
00:00:10.087083 IP 10.0.3.2.50000 > 10.0.1.1.49153: Flags [.], ack 2897, win 32768, options [TS val 10075 ecr 10025,eol], length 0
00:00:10.096702 IP 10.0.2.1.49153 > 10.0.4.2.50001: Flags [F.], seq 4345:5001, ack 1, win 32768, options [TS val 10025 ecr 10013,eol], length 656
00:00:10.099099 IP 10.0.4.2.50001 > 10.0.2.1.49153: Flags [.], ack 2897, win 32768, options [TS val 10087 ecr 10025,eol], length 0
00:00:10.108088 IP 10.0.3.2.50000 > 10.0.1.1.49153: Flags [.], ack 4002, win 32768, options [TS val 10096 ecr 10025,eol], length 0
00:00:10.108520 IP 10.0.3.2.50000 > 10.0.1.1.49153: Flags [F.], seq 1, ack 4002, win 32768, options [TS val 10096 ecr 10025,eol], length 0
00:00:10.110606 IP 10.0.1.1.49153 > 10.0.3.2.50000: Flags [.], ack 2, win 32768, options [TS val 10109 ecr 10096,eol], length 0
00:00:10.120379 IP 10.0.4.2.50001 > 10.0.2.1.49153: Flags [.], ack 4345, win 32768, options [TS val 10108 ecr 10025,eol], length 0
00:00:10.125425 IP 10.0.4.2.50001 > 10.0.2.1.49153: Flags [.], ack 5002, win 32768, options [TS val 10113 ecr 10025,eol], length 0
00:00:10.125857 IP 10.0.4.2.50001 > 10.0.2.1.49153: Flags [F.], seq 1, ack 5002, win 32768, options [TS val 10113 ecr 10025,eol], length 0
00:00:10.127944 IP 10.0.2.1.49153 > 10.0.4.2.50001: Flags [.], ack 2, win 32768, options [TS val 10126 ecr 10113,eol], length 0
```

如果你的输出结果和上面展示的结果一致（时间戳在不同的机器上可能会有些不一致，这是正常的），那么就说明你顺利完成了 Exercise 2 和 Exercise 3！

> 本 lab 的 Exercise 3,4,5 是评分项，我们提供了用于本地评测的脚本，具体的用法和细节会在本文档的第三部分介绍。不过，对于本 Exercise 而言，如果你看到 `tcpdump` 显示的结果正确，那么你的实现大概率就是正确的。

### Exercise 4 使用 Tracing 机制监测流的 cwnd

本练习中，我们将学习如何使用 ns-3 中的 Tracing 机制，并动手利用 Tracing 机制来检测流的 cwnd (Congestion Window)。本练习中我们将会用比较大的篇幅介绍 Tracing 机制，而你需要最终需要实现的代码量大约在 20 行。

运行 ns-3 模拟最重要的目的就是获取可用于研究的实验数据，而 ns-3 中获取输出的机制大致可以分为两类：

1. Pre-defined bulk output mechanisms：包括但不限于 PCAP 捕获、`NS LOG` 等。我们在 Exercise 1.3 中介绍的 PCAP 捕获就属于这类。这类机制的优点在于不需要修改任何的 ns-3 核心代码且调用起来十分方便，但缺点在于会一下子输出大量信息，往往需要你自己额外撰写使用 `grep` 、`sed` 、`awk` 等命令的脚本来处理输出，并且输出信息会占用比较大的存储空间。相信你在查看 Exercise 1.3 的输出结果时已经能想象到处理这种实验结果有多麻烦。
2. Tracing：相比前者，这一机制的优点在于 1）能够手动控制输出的实验数据的内容和格式 2）允许你按需修改 ns-3 的核心代码以获得任何你想测量的实验数据。由于这些优点，Tracing 机制被认为是 ns-3 中最最最最重要也最有用的输出机制，实际使用到的频率比第一种更高。

下面，我们就来介绍 Tracing 机制。

> 这段 Tracing 机制的介绍可能概念比较多。如果你看着觉得本文档写得令人不明所以，也可以阅读代码仓库中附有的 ns-3-tutorial.pdf 中的 8.2 和 8.3 节的英文文档，其内容涵盖了本节中介绍的所有知识。当然，你也可以在 piazza 上提出自己的疑惑，助教会密切关注并尽量迅速回复的。

ns-3 的 Tracing 机制有两个核心概念：TraceSource 和 TraceSink 。TraceSource 可以理解为 ns-3 内部的“信息生产者”，它们在 ns-3 内部模块的代码中被定义；而 TraceSink 则可以理解为用户定义的“信息消费者”，它们由用户按照自己的需求在用户代码中设计。ns-3 提供了机制将 TraceSink 和 TraceSource 关联起来，从而允许用户在用户代码中按照自己想要的方式“消费” ns-3 内部代码在模拟过程中所“生产”出来的信息。这种机制可以在概念上被理解为是回调函数。

接下来，我们讲解一个简单的例子。在该例子中，我们在 ns-3 的内部代码中定义了一个类 `MyObject` ，在 `MyObject` 中定义了两个 TraceSource。然后，我们将在用户代码中定义 TraceSink，并将其与 TraceSource 进行连接。

下面的代码展示了如何在 ns-3 内部定义 TraceSource。我们使用 `AddTraceSource` 方法来定义一个 TraceSource，其四个参数的意义如下：

1. 参数一：`name` ，类型为 `std::string` 。代表这个 TraceSource 暴露给外部的名字
2. 参数二：`help` ，类型为 `std::string` 。描述这个 TraceSource 的作用
3. 参数三：`accessor` ，类型为 `Ptr<const TraceSourceAccessor>` 。代表了 ns-3 内部代码中，TraceSink 绑定到的变量实例
4. 参数四：`callback` ，类型为 `std::string` 。描述当前 TraceSource 需要的 TraceSink 的类型

在第一个 `AddTraceSource` 中，我们定义了一个名为 `MyInteger` 的 TraceSource，并且将其绑定到了 `MyObject` 类的成员变量 `m_myInt` 上。值得注意的是， `m_myInt` 的类型是 `TraceValue<int32_t>` ，这里用到了一种 ns-3 中特殊的类型封装 `TracedValue<>`。在 ns-3 内部代码中，你仍然可以在语法上把 `m_myInt` 当作一个 `int32_t` 类型的变量来使用，不同的是，每当 `m_myInt` 内的值发生改变时（只要发生了赋值运算就算改变），TracedValue 的机制都会把 `m_myInt` 的旧值和新值（类型均为 `int32_t `一起传递给 TraceSink，从而我们就可以在 TraceSink 中记录该值的变化情况。把 TraceSource 绑定到  `TraceValue<int32_t>` 类型的变量上会要求 TraceSink 的函数签名必须为 `void (*traceSink)(int32_t oldValue, int32_t newValue);`，而这正是 `ns3::TracedValueCallback::Int32` 这一类型的定义（你可以在 `src/traced-value.h` 中找到相应的 `typedef` 语句），这也解释了第四个参数的由来。

第二个 `AddTraceSource` 语句定义的 TraceSource 和第一个不太一样。我们定义了一个名叫 `hesyCallBack` 的 TraceSource，并且将其绑定到了成员变量 `m_myCBFunction` 上。值得注意的是，`m_myCBFunction` 的类型是 `ns3::TracedCallBack< Ptr<const Packet> >` ，这里用到了另一个 ns-3 中常用的类型封装 `ns3::TracedCallback<>` 。在 ns-3 内部代码中，你可以在语法上把 `m_myCBFunction` 当作按照 `void m_myCBFunction(Ptr<const Packet>)` 定义的函数使用（所有的这类 TraceSource 函数的返回值类型必须是 `void` ），不同的是，这个“函数”的具体实现是在用户写的 TraceSink 中定义的。我们可以在用户代码中将自定义的 TraceSink 与这一 TraceSource 连接，从而指定这一 TraceSource 函数的具体实现方式。第四个参数我们传入了在 `MyObject` 中的类型 `ns3::MyObject::TracedCallback` ，之后要用户侧定义的 TraceSink 类型需要与之相符。

```C++
// 在 ns-3 内部模块的代码中定义 TraceSource
class MyObject : public Object
{
public:
  static TypeId GetTypeId (void)
  {
    static TypeId tid = TypeId ("MyObject")
      .SetParent (Object::GetTypeId ())
      .SetGroupName ("MyGroup")
      .AddConstructor<MyObject> ()
      .AddTraceSource ("MyInteger",
                       "An integer value to trace.",
                       MakeTraceSourceAccessor (&MyObject::m_myInt),
                       "ns3::TracedValueCallback::Int32")
      .AddTraceSource ("hesyCallBack",
                       "callback function get from packet.h",
                       MakeTraceSourceAccessor (&MyObject::m_myCBFunction),
                       "ns3::MyObject::TracedCallback");
    return tid;
  }
  MyObject () {}
  TracedValue<int32_t> m_myInt;
  typedef void (* TracedCallback) (Ptr<const Packet> packet);
  ns3::TracedCallback< Ptr<const Packet> > m_myCBFunction;

  void ReceivePacket (Ptr<const Packet> p) 
  {
    // You can use `m_myCBFuncton` as if it's declared by 'void m_myCBFunction(Ptr<const Packet>)'
    m_myCBFunction(p);
    // ... other operations to handle packet receive
  }
};
```

下面的代码展示了如何在用户侧定义 TraceSink 并将其与 ns-3 中的 TraceSource 连接起来。

我们在 `main` 函数中创建了一个 `MyObject` 类型的对象并记录其指针为 `myObject` ，然后使用 `TraceConnectWithoutContext` 语句将自定义的 TraceSink （分别是 `IntTrace()` 函数和 `PacketTrace()` 函数）分别连接到 ns-3 中定义的两个 TraceSource。在完成了连接后，会得到这样的效果：每当 `myObject::m_myInt` 中的值发生变化时，都会自动调用 `IntTrace()` 函数把变化的情况和发生变化的时间戳打印到标准输出；每当 ns-3 内部代码中执行 `m_myCBFunction(p)` 时，实际上都会调用 `PacketTrace(p)` 来把收到的包的大小和收到包的时间戳打印到标准输出。是不是很实用！

```C++
// 用户侧使用下面的代码将自定义的 TraceSink 与 ns-3 内部模块中的 TraceSource 相连接
void
IntTrace (int32_t oldValue, int32_t newValue)
{
  std::cout << Simulator::Now().GetSeconds() << ": From " << oldValue << " to " << newValue << std::endl;
}

void
PacketTrace (Ptr<const Packet> p)
{
  std::cout << Simulator::Now().GetSeconds() << ": Receive a packet whose size is " << p->GetSize() << " Bytes" << std::endl;
}

int
main (int argc, char *argv[])
{
  Ptr<MyObject> myObject = CreateObject<MyObject> ();
  myObject->TraceConnectWithoutContext ("MyInteger", MakeCallback (&IntTrace));
  myObject->m_myInt = 1234;

  myObject->TraceConnectWithoutContext ("hesyCallBack", MakeCallback (&PacketTrace));
}
```

上面的例子展示了在 ns-3 中使用 Tracing 机制的全流程。接下来，我们会补充一些其他细节。ns-3 中提供了一些其他注册回调函数和连接 TraceSink 与 TraceSource 的方法，下面我们简单介绍一下。

**回调函数**

下面给出了回调函数的注册方式。我们在上面介绍的例子中的就是用这里的 `MakeCallback` 来注册回调函数的。

```C++
// 回调函数
int myCallback(double){...}
// 注册回调的函数
Callback<int,double> callbackHook = MakeCallback<int,double>(myCallback);
```

还可以利用 `MakeBoundCallBack` 来注册回调函数，它的作用在于可以将回调函数的参数与某些变量绑定。例如在之前介绍的例子中，我们还像下面这样写：

```
void
PacketTrace (int arg1, float arg2, std::string arg3, Ptr<const Packet> p)
{
  std::cout << Simulator::Now().GetSeconds() << ": Receive a packet whose size is " << p->GetSize() << " Bytes" << std::endl;
  std::cout << "Welcome to the magic world of netlab! " << arg1 << arg2 << arg3;
}

int
main (int argc, char *argv[])
{
  int magic_int = 42;
  float magic_float = 3.14;
  std::string magic_str = "ilovenetlab";
  myObject->TraceConnectWithoutContext ("hesyCallBack", MakeBoundCallback (&PacketTrace, magic_int, magic_float, magic_str));
}
```

在定义 TraceSink 函数时，除了 TraceSource 定义是规定的参数列表之外，我们还可以让 TraceSink 函数额外接受 x 个形参（x 为任意值），并且在定义 TraceSink 函数时把这 x 个参数放在其参数列表的最前面。然后，我们可以使用 `MakeBoundCallback` 注册回调函数并给 `MakeBoundCallback` 额外传入 x 个实参。这样就可以把 TraceSink 函数中的前 x 个形参与 `MakeBoundCallback` 中传入的 x 个实参的值绑定。这种方法十分实用。

**Config Path**

ns-3 中的每个 TraceSource 都有一个自己的属性路径（Config Path)，例如：

```
/NodeList/7/$ns3::TcpL4Protocol/SocketList/0/CongestionWindow
```

这个属性意为：从全局结点列表中找到编号为 7 的结点的指针，并在该结点的 `ns3::TcpL4Protocol` 对象中找到 `SocketList` 属性⾥的第 0 个 socket 指针，然后找到该 socket 的 `CongestionWindow` 这一 TraceSource。

> 如果你未来想要使用其他的 TraceSource，你可以在 [ns-3文档中的 TraceSource 列表](https://www.nsnam.org/docs/release/3.38/doxygen/de/de5/_trace_source_list.html)中找到所有 ns-3 自带的 TraceSource 及其路径。当然，你也可以选择直接看源码，一般说来，每个功能模块的所有 Attribute 和 TraceSource 都会在对应的 `*.cc` 文件的开始部分被定义。

**Connect**

ns-3 中提供了如下的四种方法连接 TraceSource 和 TraceSink，区别在于 1）是否是全局的连接 2）是否传入上下文。

```C++
Config::Connect (config_path, call_back); // 给出config path以及回调函数，回调函数的第⼀个参数必须为字符串（负责存储context，也就是config_path，能更清晰的知道是哪个结点哪个属性触发了回调函数）

Config::ConnectWithoutContext (config_path, call_back); // 给出config path以及回调函数，回调函数的⽆需以context作为参数

Ptr<Object> p = ...
p->TraceConnect(attribute, call_back); // 与第⼀种类似，但不需要给出路径信息，只需要给出当前类指针的属性即可

p->TraceConnectWithoutContext(attribute, call_back); // 与第⼆种类似, 但不需要给出路径信息，只需要给出当前类指针的属性即可
```

先来看 `Config::Connect` 和 `Config::ConnectWithoutContext` 。它们的参数列表一致，第一个参数是 TraceSource 的 Config Path，第二个参数是 TraceSink 回调函数。这两个方法是“全局”的，即会把所有拥有该 TraceSource 的对象的 TraceSource 都与这个 TraceSink 相连接。它们的区别在于是否会“传入上下文”。如果使用会传入上下文的 `Config::Connect` ，则需要在 TraceSink 回调函数的参数列表中新增一个 `std::string` 类型的形参，并把它放在参数列表的第一位，之后在调用 TraceSink 函数时，会把 `Config::Connect`  中指定的 Config Path 字符串作为实参传给这第一个参数，我们在 TraceSink 回调函数中就可以通过解析 Config Path 知道是哪个节点的哪个属性触发了这次回调函数的调用。下面是使用这两种方法的示意。

```C++
// Config::ConnectWithoutContext 的用例
void TraceSinkWithoutContext(uint32_t old_value, uint32_t new_value) 
{
  // 无法得知触发本回调函数的 config path
}

void
TraceCwndWithoutContext(uint32_t nodeId, uint32_t cwndWindow)
{
    Config::ConnectWithout("/NodeList/" + std::to_string(nodeId) +
                               "/$ns3::TcpL4Protocol/SocketList/" +
                               std::to_string(cwndWindow) + "/CongestionWindow",
                               MakeCallback(&TraceSinkWithoutContext));
}

// Config::Connect 的用例
void TraceSinkWithContext(std::string context, uint32_t old_value, uint32_t new_value) 
{
  // context 参数中存放的就是触发本回调函数的 config path，本例中形如 "/NodeList/7/$ns3::TcpL4Protocol/SocketList/0/CongestionWindow"
}

void
TraceCwndWithContext(uint32_t nodeId, uint32_t cwndWindow)
{
    Config::Connect("/NodeList/" + std::to_string(nodeId) +
                        "/$ns3::TcpL4Protocol/SocketList/" +
                        std::to_string(cwndWindow) + "/CongestionWindow",
                        MakeCallback(&TraceSinkWithContext));
}

```

 `TraceConnect` 和 `TraceConnectWithoutContext` 这两个方法都是 ns-3 的基类 `Object` 类的成员函数，它们的的参数列表相同，第一个参数是 TraceSource 的名字（这里的名字不是完整的路径 Config Path，而是 TraceSource 在其调用者的类的代码中用 `AddTraceSource()` 起的名字，可以理解为这里是一个“相对路径”而非“绝对路径”），第二个参数依然是 TraceSink 回调函数。这两个方法不是“全局”的，因为它们只会把“调用者这一个实例的 TraceSource”和给定的 TraceSink 相连接。它们的区别也在于是否会传入上下文，和前面介绍的完全一样。在本节最开始给出的例子中，我们就是使用这里的方法四 `TraceConnectWithoutContext` 来进行 Tracing 连接的。

现在你已经了解了 ns-3 的 Tracing 机制，那么就来做一个小练习：利用 Tracing 机制检测哑铃型拓扑中的两条流实时的 cwnd (Congestion Window) 变化。

具体而言，你需要监测 `flow 0` 的发送方和 `flow 1` 的发送方的 cwnd 随时间的变化，并把数据分别输出到 `lv1-results/cwnd/n2.dat` 和 `lv1-results/cwnd/n3.dat` 中。两个文件的格式为：每行记录一次 cwnd 的变化，格式为 `<time> <old_cwnd> <new_cwnd>` ，分别表示发生变化的时间（以微秒为单位）、cwnd 的旧值（以 Segment 数量为大小）、cwnd 的新值（以 Segment 数量为大小）。

下面是你会需要用到的提示：

1. 你可以在理解的基础上参考 `examples/tcp/tcp-linux-reno.cc` 中监测 cwnd 的代码，这会对你非常有帮助
2. 你可以通过 `Simulator::Schedule(time, &foo, arg_1, arg_2, ..., arg_n)` 来在 ns-3 指定在某个时间以特定的参数运行某个函数。其中 `foo()` 为要运行的函数，而 `arg_1` 一直到 `arg_n` 是 `foo()` 的 n 个参数。这其实是 ns-3 进行模拟的根本方法（我们说过 ns-3 是一个基于离散事件的模拟器，每个事件其实就是这么创建出来的）
3. 在 `examples/tcp/tcp-linux-reno.cc` 的 109 行处，特地将用于连接 TraceSink 和 TraceSource 的事件指定在应用开始后的 0.001 秒。这是因为 ns-3 不能保证两个具有相同时间戳的事件被执行的先后顺序。如果用于 Tracing 连接的事件先于应用开始的事件执行，那么就会访问到一个还不存在的 socket 导致报错。你在实现自己的代码的时候也需要注意创建 Tracing 连接的时间（可以设定为 `startTime + Seconds(0.001)` 或 `startTime + TimeStep(1)`，后者更优雅一些，因为可以保证一个事件一定紧挨在另一个事件之后执行）
4. 与 `examples/tcp/tcp-linux-reno.cc` 中不同的是，本练习要求你把两条流的 cwnd 信息输出到两个不一样的文件中。你可以尝试下面的几种方式把不同的流的 cwnd 信息输出到不同的文件中：1）使用带上下文的 `Config::Connect` 语句（而非 `Config::ConnectWithoutContext`）建立连接，然后在 TraceSink 函数从第一个参数中接受 context 信息，提取出结点编号（例如使用正则表达式或直接处理字符串），从而确定输出文件的名字；2）使用 `MakeBoundCallback` 注册回调函数，使用 `TraceConnectWithoutContext` 来一个个地把回调函数绑定到每条流 ，并把结点编号作为参数一起传入；3）其他任何只要能实现相同效果的方法
5. 你可以使用 `Simulator::Now().GetMicroSeconds()` 来获取当前的模拟器时间（以微秒为单位），这个函数的返回值类型是 `int64_t`
6. 之所以输出文件分别叫 `n2.dat` 和 `n3.dat` ，是因为两条流的发送结点的全局编号分别是 2 和 3 。你可以用 `Node` 类的 `GetId()` 方法获得一个结点的全局编号
7. TraceSink 接收到的 cwnd 原始值是以字节为单位的，但我们要求输出以 Segment 数量为单位的 cwnd 大小。你可以用以字节为单位的原始值直接除以代码中设定的 segmentSize 来获得以 Segment 数量为单位的 cwnd 大小（例如 `newCwnd / segmentSize`）

如果一切顺利，你运行修改后的 `dumbbell.cc` 应该可以获得如下的输出：

```bash
student@327fb651b54a:~/workspace/ns-allinone-3.38/ns-3.38$ ./ns3 run "dumbbell --flowSize0=10000 --flowSize1=12000"
[0/2] Re-checking globbed directories...
[2/2] Linking CXX executable ../build/scratch/ns3.38-dumbbell-default
student@327fb651b54a:~/workspace/ns-allinone-3.38/ns-3.38$ ls lv1-results/cwnd/ 
n2.dat  n3.dat
student@327fb651b54a:~/workspace/ns-allinone-3.38/ns-3.38$ cat lv1-results/cwnd/n2.dat 
10025113 0 10
10064094 10 11
10088126 11 12
10112158 12 13
10136190 13 14
10160222 14 15
10184254 15 16
student@327fb651b54a:~/workspace/ns-allinone-3.38/ns-3.38$ cat lv1-results/cwnd/n3.dat 
10025577 0 10
10076110 10 11
10100142 11 12
10124174 12 13
10148206 13 14
10172238 14 15
10196270 15 16
10219214 16 17
10231230 17 18
```

你可以在记录中观察到 Reno 拥塞控制算法每次把 cwnd 增加 1 的行为。如果你能观察到类似的输出，就说明你顺利完成了 Exercise 4！

我们在评测时会将你测量出的 cwnd trace 和标准答案进行一些统计量上的比对，并允许一定的误差。具体的本地评测脚本使用方式和评测细节你可以在本文档的第三部分找到。

### Exercise 5 使用 Tracing 机制测量流的 FCT

本练习中，我们将趁热打铁，再做一个难度稍高一些的小练习：使用 ns-3 的 Tracing 机制，输出两条流的 FCT (Full Completion Time)。这一练习没有样例代码可以参考，你需要实现的代码量大约为 20-30 行。

FCT 指的是一条流从开始发送到结束所经过的时间。具体而言，对于一条流，你可以用“这条流的最后一个 ACK 报文返回到发送方的时间”减去“这条流开始的时间”来得到其 FCT。这里的“最后一个 ACK 报文”指的就是在四次挥手阶段从接收方返回发送方的 ACK 报文，如果你用 `tcpdump` 查看 Exercise 3 的输出文件的话，看到的最后一个发往流的发送方的报文就是这个报文。

你的程序需要把测出的两条流的 FCT 输出到 `lv1-results/fct/fct.dat` 中，输出含两行，每行一个整数，第一行为 `flow_0` 的 FCT，第二行为 `flow_1` 的 FCT。

下面是一些你会需要用到的提示：

1. `ApplicationContainer::Get()` 方法的放回值类型是 `Ptr<Application>` 而非 `Ptr<BulkSendApplication>` 。如果你需要的话，你可以用如下面第三行的方法来获得 `Ptr<BulkSendApplication>` 类型的值

```C++
BulkSendHelper bulk_helper(socketFactory, InetSocketAddress(address, port));
ApplicationContainer app = bulk_helper.Install(node);
Ptr<BulkSendApplication> bulk_app = app.Get(0)->GetObject<BulkSendApplication>()
```

2. 在我们的模拟场景中，你可以通过 `BulkSendApplication::GetSocket()` 得到发包应用所使用的 socket，其返回值是 `TcpSocketBase` 类型的。虽然 ns-3 中并没有哪个现存的 TraceSource 是直接用于在 socket 关闭时输出信息的，但是你可以巧妙地使用 `TcpSocketBase` 的 `Rx` 这一 TraceSource 来达成测量 FCT 的目的。你可以在 `src/internet/model/tcp-socket-base.{h,cc}` 中找到 `TcpSocketBase` 的具体实现
3. 别忘了，就像 Exercise 4 中一样，创建 Tracing 连接需要在应用开始之后进行，否则 ns-3 可能会访问到一个不存在的 socket
4. ns-3 中的 `Time` 类型可以直接进行加减运算，并且对于 `Time` 类型的对象你可以使用  `GetMicroSeconds()` 方法来获得这一时间以微秒为单位的表示，返回值的类型为 `int64_t`。例如， `Simulator::Now().GetMicroSeconds()` 就会返回以微秒为单位的当前模拟器时间
5. 新增提示：获取流的发送方的 socket 接收到最后一个报文的时间戳其实非常非常简单。其实助教设想的使用 `Rx` 这一 TraceSource 的做法中，根本没用到最后一个报文的内容具体是什么
6. 新增说明：再计算 FCT 时，你应该直接把 `startTime` （也就是 10.0s） 作为流的开始时间

如果一切顺利，你运行修改后的 `dumbbell.cc` 应该可以获得如下的输出：

```bash
student@327fb651b54a:~/workspace/ns-allinone-3.38/ns-3.38$ ./ns3 run "dumbbell --flowSize0=800000 --flowSize1=700000"
[0/2] Re-checking globbed directories...
[2/2] Linking CXX executable ../build/scratch/ns3.38-dumbbell-default
student@327fb651b54a:~/workspace/ns-allinone-3.38/ns-3.38$ ls lv1-results/fct/
fct.dat
student@327fb651b54a:~/workspace/ns-allinone-3.38/ns-3.38$ cat lv1-results/fct/fct.dat 
13034667
12403278
```

本 Exercise 可以有多种不同的实现方法，在上面的提示中所提示的的只是其中一种（也是助教用于生成标准答案的方法）。考虑到不同的实现方法测量出的结果之间可能会有一些微小的差异，我们在评测本 Exercise 时将允许你测量出的 FCT 相比于标准答案有 5% 的相对误差或者 1秒 的绝对误差。具体的本地评测脚本使用方式和评测细节你可以在本文档的第三部分找到。

## 三、提交评测

### 提交方法和评测方法

#### 提交和评测概述

本次 lab 总分 100 分。部分测试点和答案已经附在 lab4 的 starter code 中下发给各位，全部测试点会在 deadline 之后进行统一测试。

本 lab 的分数构成如下：

1. Exercise 3：每个测试点 5 分，6 个测试点，共 30 分。其中 3 个测试点已在 starter code 中公布。
2. Exercise 4：每个测试点 3 分，10 个测试点，共 30 分。其中 5 个测试点已在 starter code 中公布。
3. Exercise 5：每个测试点 4 分，10 个测试点，共 40 分。其中 5 个测试点已在 starter code 中公布。

**注意：Lab4和其他Lab1-2的评测/提交方式不同，请仔细阅读以下内容。**

**测试代码** 我们不提供 Github Classroom 评测功能，但是我们已经将本地评测的脚本和部分测试点放在了 starter code 中，具体用法将在下一节中介绍。我们只提供部分测试点。

**提交代码** 同学们可以通过 Github Classroom 提交代码。Github Classroom 不提供测试点的自动评测，因此你会在 Github Classroom 中观察到 0 分，这是正常的。

**最终评测** 最终的测试环境会在我们提供的课程容器环境中进行。最终测试时会使用全部测试点，使用的评测脚本和我们放在 starter code 中的本地评测脚本一致。

#### 如何提交

本 lab 中，你只需要提交 `dumbbell.cc` 这一个文件。你需要通过 Github Classroom 提交，具体来说，你只需要把你的 `dumbbell.cc` 文件放在本 lab 的 starter code 仓库的目录中，然后通过 Github Classroom 提交即可（和前面几个 lab 一样）。提交时你的仓库的目录结构应该如下，其中只有 `dumbbell.cc` 是你自己提交的，其余的都是本来就有的文件。更具体的说明你可以参考 starter code 中的  `README.md` 。

提醒：请务必把你提交的程序文件命名为 `dumbbell.cc` 。不然评测脚本会找不到你的答案。

```
student@327fb651b54a:~/workspace/code/lab4-starter$ tree -L 3
.
|-- README.md
|-- dumbbell-base.cc
|-- dumbbell.cc
|-- ns-3-tutorial.pdf
`-- test
    |-- README.md
    `-- test-utils
        |-- cwnd-test
        |-- fct-test
        |-- pcap-test
        `-- run_test.sh

5 directories, 6 files
```

我们的最终评测会在一台 clab 的 `elite_computing e2` 机器上运行，使用的操作系统为 `Ubuntu 22.04.5 LTS`，评测时会在评测服务器上运行课程提供的镜像环境，在镜像环境内编译运行你的 ns3 程序，并比对生成的结果和标准答案。如果你想要万无一失的话，可以自己在上面所说的评测环境中验证你提交的程序能否通过样例测试。（不过如果你在你的本地环境能通过样例测试的话，那么应该不会出大问题。）

我们在进行最终评测时，会将你提交的 `dumbbell.cc` 拷贝到评测容器中 `ns-3.38` 的 `scratch` 目录下，在 `ns-3.38` 目录下并编译运行，比对生成的结果和标准答案。我们在任务文档中提到的文件路径寻找你的程序的输出结果，所以请你务必检查，确保你的程序在每次在运行之后都能在 `ns-3.38` 的 `lv1-results` 目录下的对应路径生产正确的结果，正确的输出文件路径应该如下图所示。

**请注意：**你提交的程序**需要有在输出文件所在目录不存在的情况下创建该目录的能力。例如，即使你在运行程序前 `ns-3.38` 目录下不存在 `lv1-results` 目录，你也可以通过直接运行你的 `dumbbell` 程序来直接生成下图展示的所有目录和文件。**

```bash
student@327fb651b54a:~/workspace/ns-allinone-3.38/ns-3.38$ tree -L 3 lv1-results/
lv1-results/
|-- cwnd
|   |-- n2.dat
|   `-- n3.dat
|-- fct
|   `-- fct.dat
`-- pcap
    |-- lv1-0-0.pcap
    |-- lv1-0-1.pcap
    `-- lv1-0-2.pcap

3 directories, 6 files
```

### 本地测试的脚本及其细节

#### 使用本地评测脚本

在 starter code 中，你可以使用 `git submodule update --init --recursive` 来拉取子模块 test。

在 starter code 仓库中的 `test/test-utils` 目录下存放了大家本地测试所需要使用的脚本和测试样例。具体的用法描述可以参见 `README.md` 和 `test/test-utils/README.md` 。

你可以运行 `test/test-utils/run_test.sh` 来进行本地的测试。运行的方法形如

```bash
test/test-utils/run_test.sh <ns3_path> <test_name>
```

其中：

* `ns3_path` 表示 `ns-3.38` 目录在你的机器上的**绝对路径（不是相对路径）**
* `test_name` 表示你要进行的测试类型，支持的值有 fct、pcap、cwnd、all

举例而言，`test/test-utils/run_test.sh /home/student/workspace/ns-allinone-3.38/ns-3.38 cwnd` 可用于单独测试 Exercise 4 (cwnd)，`test/test-utils/run_test.sh /home/student/workspace/ns-allinone-3.38/ns-3.38 all` 可用于测试所有需要测试的三个 Exercise (pcap, cwnd, fct) 。具体的用法描述可以参见 `README.md` 和 `test/test-utils/README.md` 。

【补注：在 starter code 仓库的 `README.md` 和 `test/test-utils/README.md` 中，在举例介绍评测脚本运行方式的时候，把运行参数中的 `ns3_path` 打错了。请同学们以本文档的例子为准，运行脚本的第一个参数务必要写的是 `ns-3.38` 这一目录的绝对路径。】

#### 评测脚本的逻辑

下面我们特别介绍一下我们使用的评测脚本的逻辑。

> 其实，同学们如果顺利按照第二章中描述的完成了任务的话，评测下来应该就都是正确的，大概率不需要看这段介绍。之所以在此特地介绍评测脚本的逻辑，是因为本 lab 的评测脚本逻辑实在比较特殊（关于模拟器的 lab 实在是太难设计自动评测方案了）。所以我们在文档中放了这部分供同学们参考。如果大家在评测时发现评测脚本输出的结果不合理，请大胆在 piazza 上提出或者联系助教。

我们通过比对【你程序输出的模拟结果】和【我们的标准例程输出的结果】来进行评测，`test/test-utils` 目录下以 "std" 开头的那些文件就是用标准例程输出的结果。考虑到不同机器上运行模拟的结果有可能会存在一些误差，所以我们在进行评测时采取了一些宽松的允许误差的比对方式：

* Exercise 3 (PCAP)：
  我们只会比对 `lv1-0-1.pcap` 和 `lv1-0-2.pcap` 这两个文件与标准答案，比对时，我们会提取出每行的 ack 字段的值进行比对，如果都一致，那么我们就认为你的输出结果是正确的。
* Exercise 4 (cwnd)：
  在判断两个 cwnd trace 文件是否相同时，我们只会比对其中记录的 cwnd 最大值以及第一次达到 cwnd 最大值时的时间戳，并且允许 10% 的误差。
* Exercise 5 (fct)：
  在判断两个 fct 输出是否相同时，我们会比对你的输出与标准答案输出的 fct 数值，并且允许 5% 的相对误差 或 1s 的绝对误差。【补注：允许的误差范围以本文档中说的为准，不以 starter code 仓库的 `README.md` 和 `test/test-utils/README.md` 中写得有误】

#### 其它补充

我们保证所有的测试样例中的模拟都可以在设定的结束时间（60.0s）之前结束，所以你无需更改代码中的 `stopTime` 。

希望大家做 lab4 做得开心！
