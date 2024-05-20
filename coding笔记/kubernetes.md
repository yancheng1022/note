# 1、kubernetes基础


## 1.1、简介

Kubernetes 是一个开源的容器编排引擎和容器集群管理工具，用来对容器化应用进行自动化部署、 扩缩和管理。
Kubernetes 这个名字源于希腊语，意为“舵手”或“飞行员”。k8s 这个缩写是因为 k 和 s 之间有8个字符。 Google 在 2014 年开源了 Kubernetes 项目

## 1.2、云原生

Kubernetes 是 CNCF 托管的第一个开源项目。因此现在提到云原生，往往我们都把它与kubernetes联系起来。
使用Java、Go、PHP、Python等语言开发的应用我们称之为原生应用，在设计和开发这些应用时，使他们能够运行在云基础设施(或kubernetes)上，从而使应用具备可弹性扩展的能力，我们称之为云原生应用。我们可以将云原生理解为以容器技术为载体、基于微服务架构思想的一套技术体系和方法论

## 1.2、kubernetes架构

一个Kubernetes集群至少包含一个控制平面(control plane)，以及一个或多个工作节点(worker node)。

**控制平面(Control Plane) :** 控制平面负责管理工作节点和维护集群状态。所有任务分配都来自于控制平面。
**工作节点(Worker Node) :** 工作节点负责执行由控制平面分配的请求任务,运行实际的应用和工作负载。

### 1.2.1、控制平面

**kube-apiserver**
如果需要与Kubernetes 集群进行交互，就要通过 API。
`apiserver`是 Kubernetes 控制平面的前端，用于处理内部和外部请求。

**kube-scheduler**
集群状况是否良好？如果需要创建新的容器，要将它们放在哪里？这些是调度程序需要关注的问题。
`scheduler`调度程序会考虑容器集的资源需求（例如 CPU 或内存）以及集群的运行状况。随后，它会将容器集安排到适当的计算节点。

**etcd**
etcd是一个键值对数据库，用于存储配置数据和集群状态信息。

### kube-controller-manager

控制器负责实际运行集群，`controller-manager`控制器管理器则是将多个控制器功能合而为一，降低了程序的复杂性。

---

`controller-manager`包含了这些控制器：

- 节点控制器（Node Controller）：负责在节点出现故障时进行通知和响应
- 任务控制器（Job Controller）：监测代表一次性任务的 Job 对象，然后创建 Pods 来运行这些任务直至完成
- 端点控制器（Endpoints Controller）：填充端点（Endpoints）对象（即加入 Service 与 Pod）
- 服务帐户和令牌控制器（Service Account & Token Controllers）：为新的命名空间创建默认帐户和 API 访问令牌