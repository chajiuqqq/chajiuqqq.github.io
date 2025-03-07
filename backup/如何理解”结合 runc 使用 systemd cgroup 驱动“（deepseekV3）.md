“结合 runc 使用 systemd cgroup 驱动”这句话可以用一个简单的比喻来解释：

### 比喻：一个餐厅的运作
- **runc** 就像是餐厅的“厨师”。  
  它的任务是按照菜谱（OCI 规范）做出菜品（容器）。厨师负责把菜品做好，确保它们符合标准。

- **cgroup** 就像是餐厅的“资源管理员”。  
  它的任务是管理厨房的资源（比如食材、炉灶、厨师的时间等），确保每道菜（容器）都能得到它需要的资源，同时不会占用太多资源导致其他菜品（容器）受影响。

- **systemd** 就像是餐厅的“经理”。  
  它负责管理整个餐厅的运作，包括服务员、清洁工、厨房设备等。当 `systemd` 作为 cgroup 驱动时，它就接管了“资源管理员”的工作，直接管理厨房的资源分配。

### “结合 runc 使用 systemd cgroup 驱动”的含义：
这句话的意思是，让“厨师”（runc）专注于做菜（运行容器），而把资源管理的工作交给“经理”（systemd）来处理。具体来说：

1. **runc 负责运行容器**  
   - `runc` 仍然负责启动和管理容器，就像厨师专注于做菜一样。

2. **systemd 负责管理资源**  
   - 通常情况下，cgroup 直接与 Linux 内核交互来管理资源。但如果使用 systemd 作为 cgroup 驱动，容器的资源分配（比如 CPU、内存等）不再由 `cgroup` 直接管理，而是由 `systemd` 接管。就像经理负责分配厨房的资源，确保每道菜都能得到它需要的资源。
   - 在这个场景下，cgroup 并没有消失或失效，而是由 systemd 接管了 cgroup 的管理工作。换句话说，systemd 成为了 cgroup 的“上级管理者”，**资源分配仍然是通过 cgroup 机制实现的**，只是现在由 systemd 来统一控制。

3. **为什么这样做？**  
   - 如果你的餐厅（系统）是由经理（systemd）统一管理的，那么让经理来分配资源会更高效，避免资源管理的混乱。这样，餐厅的运作会更加协调，菜品（容器）和餐厅的其他部分（系统服务）都能和谐运行。

### 总结：
“结合 runc 使用 systemd cgroup 驱动”就是让 `runc` 专注于运行容器，而把资源管理的工作交给 `systemd` 来处理。这种方式特别适合在 `systemd` 管理的系统中使用，可以确保容器和系统其他部分和谐运行，就像餐厅的厨师和经理协作，让餐厅高效运作一样。
- runc：负责运行容器。
- cgroup：负责管理容器的资源。
- systemd：可以接管 cgroup 的工作，统一管理资源和系统服务。

这种协作方式就像工厂中的工人、资源分配员和总调度员一起工作，确保生产高效且有序。

## [ 配置 systemd cgroup 驱动](https://kubernetes.io/zh-cn/docs/setup/production-environment/container-runtimes/#containerd-systemd)
结合 runc 使用 systemd cgroup 驱动，在 /etc/containerd/config.toml 中设置：
```
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  ...
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true
```

## 其他驱动

**运行时驱动**

用于管理容器的运行时环境。常见的运行时驱动包括：

- runc：默认的 OCI 兼容运行时。
- crun：基于 C 语言的高性能运行时。
- kata：基于虚拟化的安全容器运行时。
- gvisor：Google 提供的用户态内核运行时。

**Cgroup 驱动**

用于管理容器的资源限制和隔离。常见的 Cgroup 驱动包括：

- cgroupfs：默认的 cgroup 驱动，直接与 cgroup 文件系统交互。
- systemd：与 systemd 集成的 cgroup 驱动，更适合与 systemd 管理的系统集成。

