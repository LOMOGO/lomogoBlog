### Kubernetes 中的基本概念和术语

kubernetes 中的大部分概念如 Pod、Node、Replication、Controller、Service 等都可以被看作一种资源对象，几乎所有的资源对象都可以通过 Kubernetes 提供的 kubectl 或者 api 执行增删改查等操作并保存在 etch 中持久化存储。

Kubernetes 通过跟踪对比 etcd 库里保存的“资源期望状态”与当前环境中的“实际资源状态”的差异来实现自动控制和自动纠错的功能。

apiVersion 是 Kubernetes 资源对象中一个关键的属性，大部分常见的核心资源对象都归属于 v1 这个核心 API ，例如：Node、Pod、Service、Endpoints、Namespace、RC、PersistentVolume等。在 Kubernetes 1.9 之后引入了 apps/v1 这个拓展的 API 组，从而正式淘汰了 extensions/v1beta1、apps/v1beta1、apps/v1beta2 这三个 API 组。

Kubernetes 里的所有资源对象都可以采用 YAML 或者 JSON 格式的文件来定义或描述。

