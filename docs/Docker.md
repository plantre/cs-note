###### 应用移植过程中环境一致性问题，提供标准化的软件打包、分发的能力

###### 解决了如何让应用从一个软件环境转移到另一个软件环境时可靠运行的问题



## 原理

### 基于 LXC（Linux container- Linux 容器）虚拟容器技术

#### LXC 技术主要是借助 Linux 内核中提供的 CGroup 功能和 namespace 来实现的

namespace 是 Linux 内核用来隔离内核资源的方式，为了隔离进程组之间的资源

CGroup 是 Control Groups 的缩写，是 Linux 内核提供的一种可以限制、记录、隔离进程组 (process groups) 所使用的物力资源 (如 cpu memory i/o 等等) 的机制。，为了对一组进程进行统一的资源监控和限制