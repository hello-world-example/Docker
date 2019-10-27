# cgroups



> - **namespace** : linux 内核在 task_struct 中对进程组管理的基础机制
> - **cgroups** ( **Control Groups** ) : namespace 的用户空间的管理接口
> - **LXC** ( **Linux Container** ) : cgroups 的管理工具
> - **Docker** : LXC 的管理器
>
> 
>
> **Docker** 是 Go 来实现的，动化了对 **LXC** 的管理过程，能够自动在线下载相应的发行版本的 **rootfs**。**LXC** 可以直接 **chroot** 到任意的系统的 **rootfs** 上并通过 **cgroups** 的限制机制来控制容器内系统的资源占有率。**cgroups** 通过配置文件或者命令配置后，限制相应进程或一组进程使用的系统资源。
>
> 摘录自 —— [docker lxc cgroup namespace 入门](https://blog.csdn.net/cnsword/article/details/17053865)



## 作用

- **资源限制（Resource Limitation）**：cgroups 可以对进程组使用的资源总额进行限制。如设定应用运行时使用内存的上限，一旦超过这个配额就发出 OOM（Out of Memory）。
- **优先级分配（Prioritization）**：通过分配的 CPU 时间片数量及硬盘 IO 带宽大小，实际上就相当于控制了进程运行的优先级。
- **资源统计（Accounting）**： cgroups 可以统计系统的资源使用量，如 CPU 使用时长、内存用量等等，这个功能非常适用于计费。
- **进程控制（Control）**：cgroups 可以对进程组执行挂起、恢复等操作。



## 子系统

- blkio  :   这个子系统设置限制每个块设备的输入输出控制。例如:磁盘，光盘以及usb等
- cpu   :   这个子系统使用调度程序为cgroup任务提供cpu的访问
- cpuacct :   产生cgroup任务的cpu资源报告
- cpuset  :   如果是多核心的cpu，这个子系统会为cgroup任务分配单独的cpu和内存
- devices :   允许或拒绝cgroup任务对设备的访问
- freezer :   暂停和恢复cgroup任务
- memory  :   设置每个cgroup的内存限制以及产生内存资源报告
- net_cls :   标记每个网络包以供cgroup方便使用
- ns    :   名称空间子系统
- perf_event:  增加了对每group的监测跟踪的能力



## 常用命令

```bash
# 安装 (apt-get install cgroup-bin)
yum install libcgroup

# 显示已经挂载的子系统 ( lssusys )
ll /sys/fs/cgroup/ 

..
```




## Read More
- https://www.kernel.org/doc/html/latest/admin-guide/cgroup-v2.html
- https://www.kernel.org/doc/html/latest/admin-guide/cgroup-v1/index.html
- 
- [Docker 资源管理探秘：Docker 背后的内核 Cgroups 机制](https://www.infoq.cn/article/docker-resource-management-cgroups)
- [孙健波](https://www.infoq.cn/profile/1279534)  15.03.12 [Docker 背后的内核知识——Namespace 资源隔离](https://www.infoq.cn/article/docker-kernel-knowledge-namespace-resource-isolation)
- [孙健波](https://www.infoq.cn/profile/1279534)  15.04.20 [Docker 背后的内核知识——cgroups 资源限制](https://www.infoq.cn/article/docker-kernel-knowledge-cgroups-resource-isolation)
- [孙健波](https://www.infoq.cn/profile/1279534)  15.06.02 [Docker 背后的容器管理——Libcontainer 深度解析](https://www.infoq.cn/article/docker-container-management-libcontainer-depth-analysis)
- [孙健波](https://www.infoq.cn/profile/1279534)  15.10.09 [Docker 背后的标准化容器执行引擎——runC](https://www.infoq.cn/article/docker-standard-container-execution-engine-runc)

