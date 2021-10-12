## Pod

容器的本质是进程，Kubernetes 本质上类似操作系统。在一个 Linux 操作系统里，进程并不是一个个孤立运行。而是以进程组的方式，有原则的组织在一起。Pod 是 Kubernetes 项目的原子调度单位，Kubernetes 项目所做的，就是将 “进程组” 的概念映射到了容器技术（抽象成了 Pod）中，使 Pod 成为了这个云计算 “操作系统” （k8s）里的 “一等公民”。而 Kubernetes 这个“操作系统”可以依据 Pod 所需要的资源灵活的为 Pod 选择合适的工作节点去运行。

容器与容器之间可能会存在一些紧密的运行关系，因此有时他们需要部署到同一台“机器”上，可以把 Pod 理解为一个“机器”。在一个 Pod 中可以运行多个容器，这些容器可以共享这台“机器”( Pod )上的资源。

### 实现原理

Pod 其实只是一个逻辑上的概念，Pod 本身并没有创造出一个“物理”上的隔离环境。因此 Kubernetes 中真正运行的还是 Pod 中的容器。Pod 中的所有容器，都共享了同一个 Network Namespace，并且可以声明共享同一个 Volume。

#### Pod 中的容器的启动关系

一个 Pod 中用户的多个容器是对等（没有先后顺序）的启动关系。要实现这种效果可以通过在这些容器启动之前先启动一个额外的容器。在 Kubernetes 中，Pod 的实现需要使用一个中间容器：Infra 容器。Infra 容器是 Pod 中第一个被创建的容器，用户自定义的其他容器则通过 join Network Namespace 的方式和 Infra 容器关联在一起。Pod 的生命周期和 Infra 容器一致，和容器 A 与 B 无关。

如果想要用户容器按照顺序启动，可以借助 Init Container 来实现。Init Container 中定义的容器会按照顺序逐一启动，并且直到他们启动完毕后 spec.containers 中定义的用户容器才会启动。

### Pod 所拥有的一些属性

Pod 是 Kubernetes 中的最小的调度单位，那么它也就拥有一些属性。而容器是具体运行的进程，那么容器也应当拥有一些属性。Pod 和容器各自拥有一些属性。可以将 Pod 理解为一个运行程序的“机器”这个层面，而容器就是机器上运行的程序。这样就可以比较自然的理解 Pod 对象、以及容器所拥有的一些属性了。因此凡是调度、网络、存储、Namespace、以及安全相关的属性基本属于 Pod 级别。下面将介绍 Pod 中一些比较重要的字段。

- nodeSelector

  这个字段可以将 Pod 和工作节点进行绑定，用法如下：

  ```yaml
  apiVersion: v1
  kind: Pod
  ...
  spec:
    nodeSelector:
      disktype: ssd
  ```

  这样这个 Pod 只能运行在携带了 "disktype: ssd" 标签（Label）的节点上，否则将调度失败。

- nodeName

  当 Pod 的这个字段被赋值之后，Kubernetes 调度器就会认为这个 Pod 已经被这个字段代表的节点调度过了。一般用于测试的时候使用。

- hostAliases

  定义了 Pod 中 /etc/hosts 文件的内容

- shareProcessNamespace

  当 shareProcessNamespace 的值为 true 的时候，Pod 中的容器将共享同一个 PID Namespace 。

- hostNetwork

  当 shareProcessNamespace 的值为 true 的时候，Pod 中的容器将共享宿主机的 Network。即这些容器可以使用宿主机的网络。

- hostIPC

  当 hostIPC 的值为 true 的时候， Pod 中的容器将共享将共享宿主机的 IPC。即这些容器可以直接与宿主机进行 IPC 通信。

- hostPID

  当 hostPID 的值为 true 的时候， Pod 中的容器将共享将共享宿主机的 PID。即这些容器可以看到宿主机中运行的所有进程。
