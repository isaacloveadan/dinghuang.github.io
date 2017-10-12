---
layout: default
title:  "OpenShift学习"
date:   2017-10-11 17:50:00
categories: main
---
目录
==

* TOC
{:toc}

[OpenShift官方文档](https://docs.openshift.com/enterprise/3.0/welcome/index.html)

#   OpenShift 起步

1.OpenShift历史
--------------------------------------------------

### 1.1. OpenShift介绍

![此处输入图片的描述][1]

OpenShift空间是Red Hat的平台即服务的云计算平台(PaaS)，自成立以来就为大家提供免费空间服务，深受到国内外用户的欢迎。虽然全世界的免费云空间商从2010年开始就如雨后春笋般地出现，但是真正能够从激烈的竞争环境中生存下来的也就是那几个实力雄厚的公司的产品。

Amazon EC2、Google Compute Engine、Windows Azure是我们熟知的亚马逊、谷歌和微软三家网络巨头公司云计算服务产品，都为我们提供的空间和VPS主机的试用服务，但是很遗憾的就是这三家公司的云空间都需要使用信用卡才能申请使用，有的国内申请了还不能正常使用。

OpenShift免费空间是部落介绍的所有的免费空间中真正可以让人放心使用的产品，2012年十大优秀免费空间、2013年十大免费空间综合排行榜、2014年十大优秀免费空间排行连续三年被部落读者评选为最让人期待的免费空间服务，也是这么多年来一直被我们免费使用的云计算服务。

2.OpenShift架构
--------------------------------------------------

### 2.1. 什么是Layers？

Docker提供了包装和创建基于Linux的轻型容器的抽象 。Kubernetes提供 集群管理，并在多个主机上协调Docker容器。

OpenShift补充说：

 - 开发人员的源代码管理， 构建和 部署
 - 在流经您的系统时，管理和推广 Image
 - 应用程序管理规模
 - 组织大型开发人员组织的团队和用户跟踪

![此处输入图片的描述][2]

### 2.2. 什么是OpenShift架构？

OpenShift具有基于微服务的较小的，去耦合的单元的架构。它可以在Kubernetes集群的顶部（或旁边）运行，还可以 存储有关存储在etcd中的对象的数据， 这是一个可靠的集群键值存储。这些服务按功能细分：

REST API，它暴露了每个 核心对象。

读取这些API的控制器对其他对象应用更改，并报告状态或回写对象。

用户调用REST API来更改系统的状态。控制器使用REST API来读取用户期望的状态，然后尝试使系统的其他部分同步。例如，当用户请求 构建时，它们创建一个“构建”对象。构建控制器看到已经创建了一个新构建，并在集群上运行一个进程来执行该构建。构建完成后，控制器将通过REST API更新构建对象，并且用户看到其构建完成。

控制器模式意味着OpenShift中的大部分功能是可扩展的。运行和启动的方式可以独立于Image的管理方式或部署的发生方式进行定制 。控制器正在执行系统的“业务逻辑”，采取用户行动并将其转化为现实。通过定制这些控制器或用自己的逻辑替换它们，可以实现不同的行为。从系统管理的角度来看，这也意味着API可以用于在重复的日程表上对常见的管理操作进行脚本编写。这些脚本也是监视更改并执行操作的控制器。OpenShift能够以这种方式定制集群的一流行为。

为了实现这一点，控制器利用可靠的系统更改流将系统的视图与用户正在进行的操作进行同步。该事件流会在更改发生时立即将更改从etcd推送到REST API，然后将其更改为控制器，从而可以快速有效地通过系统进行更改。但是，由于故障可以随时发生，控制器还必须能够在启动时获取系统的最新状态，并确认所有内容都处于正确的状态。这种重新同步是重要的，因为这意味着即使出现问题，操作员也可以重新启动受影响的组件，系统会在继续之前对其进行双重检查。系统应该最终收敛到用户的意图，因为控制器可以随时使系统同步。

### 2.3. OpenShift如何保护？

OpenShift和Kubernetes API会 验证提供凭据的用户，然后 根据角色授权他们。开发人员和管理员都可以通过多种方式进行身份验证，主要是 OAuth令牌和SSL证书授权。

开发商（该系统的客户端）通常使来自REST API调用 客户端程序像oc或到 Web控制台通过他们的浏览器，并使用OAuth承载令牌大多数通信。基础架构组件（如节点）使用系统生成的包含其身份的客户端证书。在容器中运行的基础架构组件使用与其服务帐户相关联的令牌 连接到API。

授权在OpenShift策略引擎中处理，该引擎定义了诸如“创建pod”或“列表服务”之类的操作，并将它们分组为策略文档中的角色。角色由用户或组标识符绑定到用户或组。当用户或服务帐户尝试执行操作时，策略引擎将在允许其继续之前检查分配给用户的一个或多个角色（例如，当前项目的集群管理员或管理员）。

由于集群上运行的每个容器都与服务帐户相关联，因此也可以将秘密与这些服务帐户相关联 并将其自动传递到容器中。这使得基础设施可以管理用于拉和推送Image，构建和部署组件的秘密，并且还允许应用程序代码轻松利用这些秘密。

3.OpenShift基础架构组件
--------------------------------------------------

### 3.1. Kubernetes

在OpenShift中，Kubernetes通过一组容器或主机管理集装箱化应用程序，并提供部署，维护和应用程序扩展的机制。 Docker包，实例化和运行容器化应用程序。

Kubernetes集群由一个或多个主节点和一组节点组成。

### 3.2. Masters

Masters是主机或包含所述主组件，包括API服务器，控制器管理器服务器和主机ETCD。主管理器管理 其Kubernetes集群中的节点，并调度 pod以在节点上运行。

|-----------------+------------|
| 零件        | 描述   |
|-----------------|:-----------|
| API服务器     | Kubernetes API服务器验证和配置pod，服务和复制控制器的数据。它还将pod分配给节点，并将pod信息与服务配置同步。 |
| ETCD        |   etcd存储持续的主状态，而其他组件监视etcd 进行更改以使其进入所需状态。etcd可以可选地配置为高可用性，通常部署于2n + 1对等服务。  |
| Controller Manager Server       |    控制器管理器服务器监视etcd以更改复制控制器对象，然后使用API​​强制执行所需的状态。    | 
| Pacemaker（起搏器）     | 可选，配置 [高可用主机时使用][3]。Pacemaker是红帽企业Linux高可用性附件的核心技术 ，提供consensus，fencing和服务管理。它可以在所有主机上运行，​​以确保所有主动 - 被动组件都运行一个实例。 |
|-----------------+------------|

### 3.3. 高可用性Masters

在单个主配置中，如果主服务器或其任何服务失败，则运行的应用程序的可用性将保留。然而，主服务的故障降低了系统响应应用程序故障或创建新应用程序的能力。您可以选择为高可用性配置主机，以确保集群没有单点故障。

为了减轻对masters的可用性的担忧，建议使用两种活动：

一个运行手册条目应该用于重建主创建。运行本条目是任何高可用性服务的必要支持。额外的解决方案仅仅是控制必须查阅运行手册的频率。例如，主主机的冷待机可以充分满足SLA，SLA需要不超过几分钟的停机时间来创建新应用程序或恢复失败的应用程序组件。

使用高可用性解决方案配置主设备，并确保集群没有单点故障。的 高级安装方法提供了使用具体示例Pacemaker作为管理技术，该技术红帽建议。但是，您可以将这些概念应用于现有的高可用性解决方案。

不支持在安装后从单个主群集迁移到多个主群。
使用Pacemaker时，主组件具有以下可用性：
Availability Matrix

|-----------------+------------+------------|
| Role        | Style   | Notes   |
|-----------------|:-----------|:-----------|
| ETCD     | 主动 - 主动 | 通过负载均衡完全冗余部署 |
|主服务     | 主动 - 被动 | 一时间由Pacemaker管理 |
| Pacemaker   |主动 - 主动 | 完全冗余部署 |
| 虚拟IP     | 主动 - 被动 | 一时间由Pacemaker管理 |
|-----------------+------------|

![此处输入图片的描述][4]

### 3.4. Nodes（节点）


节点为容器提供运行时环境。Kubernetes集群中的每个节点都具有要由主节点管理的所需服务 。节点还具有运行pod的所需服务，包括Docker，kubelet和服务代理。

OpenShift从云提供商，物理系统或虚拟系统创建节点。Kubernetes与 这些节点的表示的节点对象进行交互。主人使用来自节点对象的信息来验证具有健康检查的节点。一个节点被忽略，直到它通过运行状况检查，并且主节点继续检查节点，直到它们有效。该 Kubernetes文档对节点管理的更多信息。

管理员可以使用CLI 管理 OpenShift实例中的节点。要在启动节点服务器时定义完整的配置和安全选项，请使用 [专用节点配置文件][5]。

### 3.5. Kubelet

每个节点都有一个kubelet来更新由容器清单指定的节点，该容器清单是描述一个pod的YAML文件。kubelet使用一组清单来确保其容器启动并继续运行。示例清单可以在Kubernetes文档中找到 。

可以通过以下方式向kubelet提供容器清单：

 - 每20秒检查一次命令行上的文件路径。
 - 在命令行上传递HTTP端点，每20秒检查一次。
 - 观察一个`etcd`服务器的`kubelet`，例如`/ registry / hosts / $（hostname -f）`，并对任何更改进行操作。
 - 监听HTTP的kubelet响应一个简单的API来提交一个新的清单。

### 3.6. Service Proxy（服务代理）

每个节点还运行一个简单的网络代理，以反映在该节点上的API中定义的服务。这允许节点在一组后端执行简单的TCP和UDP流转发。

### 3.7. Node Object Definition（节点对象定义）

以下是Kubernetes中的一个示例节点对象定义：

    apiVersion: v1  【1】
    kind: Node 【2】
    metadata:
      creationTimestamp: null
      labels: 【3】
        kubernetes.io/hostname: node1.example.com
      name: node1.example.com 【4】
    spec:
      externalID: node1.example.com 【5】
    status:
      nodeInfo:
        bootID: ""
        containerRuntimeVersion: ""
        kernelVersion: ""
        kubeProxyVersion: ""
        kubeletVersion: ""
        machineID: ""
        osImage: ""
        systemUUID: ""
        
    

 1. `apiVersion` 定义要使用的API版本。
 2. `kind`设置为将其Node标识为节点对象的定义。
 3. `metadata.labels`列出已添加到节点的任何 标签。
 4. `metadata.name`是定义节点对象名称的必需值。`NAME`运行`oc get nodes` 命令时，该列显示在该列中。
 5. `spec.externalID`定义可以到达节点的完全限定域名。`metadata.name`空时默认为该值。

### 3.8. Image Registry

OpenShift可以使用实现Docker registryAPI的任何服务器作为Image来源，包括规范的Docker Hub，由第三方运行的私人Registry以及集成的OpenShift Registry。

**集成OpenShift Registry**

OpenShift提供了一个集成的Docker Registry，增加了快速配置新Image存储库的能力。这允许用户自动为其构建的地方 推送生成的Image。

每当将新Image推送到集成Registry时，Registry通知OpenShift关于新Image，传递所有关于它的信息，例如命名空间，名称和Image元数据（namespace, name, and image metadata）。OpenShift的不同部分对新Image做出反应，创建新的构建和部署。

**第三方Registry管理机构**

OpenShift可以使用来自第三方Registry的Image创建容器，但这些Registry不太可能提供与集成的OpenShift Registry相同的Image通知支持。在这种情况下，OpenShift将在创建Image流时从远程Registry中获取标签。刷新获取的标签就像运行一样简单`oc import-image <stream>`。当检测到新Image时，会发生先前描述的构建和部署反应。

要从外部Registry创建Image流，请`spec.dockerImageRepository`适当地设置该字段。例如：

    {
      "apiVersion": "v1",
      "kind": "ImageStream",
      "metadata": {
        "name": "ruby"
      },
      "spec": {
        "dockerImageRepository": "openshift/ruby-20-centos7"
      }
    }
    
OpenShift同步标签和 image metadata 后，看起来像这样：

    {
      "kind": "ImageStream",
      "apiVersion": "v1",
      "metadata": {
        "name": "ruby",
        "namespace": "default",
        "selfLink": "/osapi/v1/namespaces/default/imagestreams/ruby",
        "uid": "9990ea5f-f35a-11e4-937e-001c422dcd49",
        "resourceVersion": "53",
        "creationTimestamp": "2015-05-05T19:11:57Z",
        "annotations": {
          "openshift.io/image.dockerRepositoryCheck": "2015-05-05T19:12:00Z"
        }
      },
      "spec": {
        "dockerImageRepository": "openshift/ruby-20-centos7"
      },
      "status": {
        "dockerImageRepository": "openshift/ruby-20-centos7",
        "tags": [
          {
            "tag": "latest",
            "items": [
              {
                "created": "2015-05-05T19:11:58Z",
                "dockerImageReference": "openshift/ruby-20-centos7:latest",
                "image": "94439378e4546d72ef221c47fe2ac30065bcc3a98c25bc51bed77ec00efabb95"
              }
            ]
          },
          {
            "tag": "v0.4",
            "items": [
              {
                "created": "2015-05-05T19:11:59Z",
                "dockerImageReference": "openshift/ruby-20-centos7:v0.4",
                "image": "c7dbf059225847a7bfb4f40bc335ad7e70defc913de1a28aabea3a2072844a3f"
              }
            ]
          }
        ]
      }
    }
    
查询外部Registry以同步标签和 image metadata当前不是一个自动化过程。要手动重新同步，请运行 `oc import-image <stream>`。在短时间内，OpenShift将与外部Registry进行通信，以获取有关与image流相关联的DockerImage存储库的最新信息。

**Authentication(认证)**

OpenShift可以与Registry进行通信，以使用用户提供的凭证访问私有image存储库。这允许OpenShift将image从私有存储库中推出。该[认证][6]主题有更多的信息。

### 3.9. Web Console

OpenShift Web控制台是可从Web浏览器访问的用户界面。开发人员可以使用Web控制台来显示，浏览和管理项目的内容。

Web控制台作为master的一部分启动 。`openshift`二进制文件提供运行Web控制台所需的所有静态资产。管理员还可以使用扩展来[定制Web控制台][7]，这样可以在Web控制台加载时运行脚本并加载自定义样式表。您可以通过这种方式改变用户界面几乎任何方面的外观和感觉。

当您从浏览器访问Web控制台时，它首先加载所有必需的静态资源。然后，使用从该`openshift start`选项 `--public-master`或相关 主配置文件参数定义的值向OpenShift API发出请求`masterPublicURL`。Web控制台使用WebSockets来维护与API服务器的持久连接，并在可用时立即接收更新的信息。

必须启用JavaScript才能使用Web控制台。为获得最佳体验，请使用支持[WebSockets][8]的Web浏览器 。

![此处输入图片的描述][9]

配置的主机名和Web控制台的IP地址列入白名单，以便安全访问API服务器，即使浏览器会将请求视为跨原点。要从使用不同主机名的Web应用程序访问API服务器，必须通过在相关的 [主配置文件参数][10]`--cors-allowed-origins`上指定选项来将该主机名列入白名单。`openshift startcorsAllowedOrigins`

**Project Overviews**

![此处输入图片的描述][11]

 1. 项目选择器允许您在有权访问的项目之间进行 切换。
 2. 使用资源的标签过滤项目页面的内容 。
 3. 使用源存储库或 使用模板创建新的应用程序 。
 4. “ 概览”选项卡（当前选中）可以使用每个组件的高级视图来可视化项目的内容。
 5. “ 浏览”选项卡可以探索项目中不同的 对象类型： 构建， 部署， Image流， Pods和 服务。
 6. “ 设置”选项卡提供有关项目的一般信息，以及在项目中设置的配额和 资源限制。
 7. 当您在“ 概述”页面中单击其中一个对象时，“ 详细信息” 窗格将显示有关该对象的详细信息。在本示例中，选择了
    cakephp-mysql示例部署，“ 详细信息”窗格将显示相关复制控制器上的详细信息 。

**JVM控制台**

此功能目前在 技术预览中，不适用于生产用途。
对于基于JavaImage的pod，Web控制台还可以访问 基于[hawt.io][12]的JVM控制台，以查看和管理任何相关的集成组件。只要容器有一个名为jolokia的端口，Connect 链接将显示在Browse → Pods page页面上的“窗格”的详细信息中。

![此处输入图片的描述][13]

连接到JVM控制台后，会显示不同的页面，具体取决于与连接的pod相关的组件。
![此处输入图片的描述][14]

以下页面可用：

|-----------------+------------|
| Page        | 描述   |
|-----------------|:-----------|
| JMX   | 查看和管理JMX域和mbeans。 |
| Threads        |   查看和监视线程的状态。  |
| ActiveMQ       |    查看和管理Apache ActiveMQ brokers。    | 
| Camel     | 查看和管理Apache Camel路由和依赖关系。 |
|-----------------+------------|

4.OpenShift核心概念
--------------------------------------------------

### 4.1. 概观

以下主题提供有关使用OpenShift时将遇到的核心概念和对象的高级架构信息。许多这些对象来自Kubernetes，它由OpenShift扩展，以提供更多功能丰富的开发生命周期平台。

 - Containers and images是部署应用程序的基础。
 - Pods and services 允许容器相互通信和代理连接。
 - Projects and users 提供了社区组织和管理其内容的空间和手段。
 - Builds and image streams 允许您构建工作Image并对新Image做出反应。
 - Deployments 增加了对软件开发和部署生命周期的扩展支持。
 - Routes 向世界宣布您的服务。
 - Templates 允许根据自定义参数一次创建多个对象。

### 4.2. Containers and images

**Containers**

OpenShift应用程序的基本单位称为容器。 Linux容器技术 是用于隔离正在运行的进程的轻量级机制，因此它们仅限于与其指定的资源进行交互。许多应用程序实例可以在单个主机上的容器中运行，而不会看到彼此的进程，文件，网络等。通常，每个容器提供单一服务（通常称为“微服务”），例如Web服务器或数据库，尽管容器可用于任意工作负载。

Linux内核多年来一直在集成容器技术的功能。最近 Docker项目为主机上的Linux容器开发了一个方便的管理界面。OpenShift和Kubernetes增加了在多主机安装中编排Docker容器的能力。

尽管您在使用OpenShift时不直接与Docker工具进行交互，但是了解Docker的功能和术语对于了解OpenShift中的角色以及应用程序在容器内的作用是非常重要的。Docker可以作为RHEL 7以及CentOS和Fedora的一部分，因此您可以从OpenShift中单独进行实验。请参阅 [Red Hat Systems上的Docker格式化容器Image入门指南][15]，以作指导性介绍。

**Docker Images**

Docker容器基于Docker Image。Docker Image是包含运行单个Docker容器的所有要求的二进制文件，以及描述其需求和功能的元数据。您可以将其视为包装技术。Docker容器只能访问Image中定义的资源，除非您在创建容器时给予容器附加访问权限。通过在多个主机上的多个容器中部署相同的Image并在它们之间进行负载平衡，OpenShift可以为打包到Image中的服务提供冗余和水平缩放。

您可以直接使用Docker来构建Image，但OpenShift还提供构建者，通过将代码或配置添加到现有Image来帮助创建Image。

由于应用程序随着时间的推移而发展，因此单个Image名称实际上可以引用许多不同版本的“相同”Image。每个不同的Image通过其散列（例如长十六进制数`fd44297e2ddb050ec4f…​`）唯一地引用，通常缩短为12个字符（例如`fd44297e2ddb`）。而不是版本号，Docker允许应用标签（如`v1`，`v2.1`，`GA`，或默认`latest`），除了Image名称进一步指定Image需要的话，你可能会看到相同的Image被称为`centos`（意味着`latest` 标记），`centos:centos7`或`fd44297e2ddb`。

**Docker Registries**

Docker registry 是用于存储和检索Docker Image的服务。registry 包含一个或多个Docker Image存储库的集合。每个Image库包含一个或多个标记的Image。Docker提供自己的registry [Docker Hub][16]，但您也可以使用私人或第三方registry。Red Hat registry.access.redhat.com为订阅者提供了一个Docker registry 。OpenShift还可以提供自己的内部registry 来管理自定义Docker Image。

Docker容器，Image和registry 之间的关系如下图所示：
![此处输入图片的描述][17]

**Pods and Services**

OpenShift利用Kubernetes的概念pod，它是一个或多个在一个主机上部署的容器，以及可以定义，部署和管理的最小计算单元。

Pods是OpenShift v2 gears的粗略的等同物，容器相当于v2 cartridge 实例。每个pod分配了自己的内部IP地址，因此拥有其整个端口空间，pod中的容器可以共享其本地存储和网络。

Pods具有生命周期; 它们被定义，则它们被分配为在节点上运行，然后它们运行直到其容器退出或者由于某种其他原因被移除。取决于策略和退出代码的Pods可能会在退出后被删除，也可能被保留，以便访问其容器的日志。

OpenShift将Pods视为绝对不变的; pod定义在运行时不能进行更改。OpenShift通过终止现有的pod并通过修改的配置，基本Image或两者重新创建它来实现更改。Pods也被视为消耗品，并且在重新创建时不保持状态。因此，pod通常由更高级别的控制器管理，而不是由用户直接管理。

以下是提供长时间运行的服务的pod的示例定义，这实际上是OpenShift基础设施的一部分：私有Docker registry。它展示了pod的许多功能，其中大部分在其他主题中讨论，因此在此仅作简单介绍：

    apiVersion: v1
    kind: Pod
    metadata:
      annotations: { ... }
      labels:  【1】                              
        deployment: docker-registry-1
        deploymentconfig: docker-registry
        docker-registry: default
      generateName: docker-registry-1-       【2】   
    spec:
      containers:              【3】                 
      - env:                【4】                    
        - name: OPENSHIFT_CA_DATA
          value: ...
        - name: OPENSHIFT_CERT_DATA
          value: ...
        - name: OPENSHIFT_INSECURE
          value: "false"
        - name: OPENSHIFT_KEY_DATA
          value: ...
        - name: OPENSHIFT_MASTER
          value: https://master.example.com:8443
        image: openshift/origin-docker-registry:v0.6.2 【5】   
        imagePullPolicy: IfNotPresent
        name: registry
        ports:                【6】                 
        - containerPort: 5000
          protocol: TCP
        resources: {}
        securityContext: { ... }           【7】    
        volumeMounts:                【8】          
        - mountPath: /registry
          name: registry-storage
        - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
          name: default-token-br6yz
          readOnly: true
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: default-dockercfg-at06w
      restartPolicy: Always
      serviceAccount: default               【9】   
      volumes:                           【10】      
      - emptyDir: {}
        name: registry-storage
      - name: default-token-br6yz
        secret:
          secretName: default-token-br6yz

 1. Pods可以用一个或多个标签“标记”，然后可以在单个操作中选择和管理Pods组。标签以metadata哈希中的密钥/值格式存储。此示例中的一个标签是`docker-registry= default`。
 2. Pods必须在其命名空间中具有唯一的名称。pod定义可以指定具有generateName属性的名称的基础，并且将自动添加随机字符以生成唯一的名称。
 3. containers指定容器定义的数组;在这种情况下（和大多数情况一样），只有一个。
 4. 可以指定环境变量，将必需的值传递给每个容器。
 5. pod中的每个容器都是从其自己的Docker Image中实例化的 。 
 6. 容器可以绑定到将在pod的IP上可用的端口。
 7. OpenShift 为容器定义了一个[security context][18] ，指定它们是否被允许作为特权容器运行，作为他们选择的用户运行等等。默认上下文是非常严格的，但管理员可以根据需要进行修改。
 8. 容器指定在容器内装载外部存储卷的位置。在这种情况下，存在用于存储registry数据的卷，一个用于访问registry对OpenShift
    API进行请求所需的凭据的卷。
 9. 针对OpenShift API提出请求的pod是一个常见的模式，有一个serviceAccount字段用于指定pod应该在发出请求时应验证哪个服务帐户用户。这样可以实现对自定义基础架构组件的细粒度访问控制。
 10. pod定义了可供其容器使用的存储卷。在这种情况下，它为registry storage 提供了一个短暂的volume，以及secret包含服务帐户凭据的volume。

**Services**
Kubernetes service作为内部负载均衡器。它识别一组复制的pod，以便代理它接收的连接。在服务保持一贯可用的情况下，可以任意添加备份pod或从服务中删除服务，使任何依赖于服务的内容能够在一致的内部地址处引用。

服务被分配一个IP地址和端口对，当被访问时，代理到适当的后备Pod。服务使用标签选择器来查找在某个端口上提供某种网络服务的所有运行的容器。

像pod一样，服务是REST对象。以下示例显示了上面定义的服务的定义：
 

    apiVersion: v1
    kind: Service
    metadata:
      name: docker-registry      【1】
    spec:
      selector:                  【2】
        docker-registry: default
      portalIP: 172.30.136.123   【3】
      ports:
      - nodePort: 0
        port: 5000               【4】
        protocol: TCP
        targetPort: 5000         【5】
        

 1. 服务名称docker-registry还用于构造一个带有服务IP的环境变量，该服务IP插入到同一命名空间中的其他pod中。最大名称长度为63个字符。
 2. 标签选择器标识所有pods，其中附加了 docker-registry = default标签作为其后备pod。
 3. 服务的虚拟IP，从内部IP池创建时自动分配。
 4. 端口服务侦听。
 5. 服务转发连接的后台上的端口。

该 [Kubernetes文件][19]对服务的更多信息。

**Labels**

标签用于组织，分组或选择API对象。例如，pod用标签“标记”，然后 服务使用标签选择器来标识它们代理的pod。这使得服务可以引用Pod组，甚至将具有潜在不同的Docker容器的Pod作为相关实体进行处理。

大多数对象可以在其元数据中包含标签。因此，标签可用于分组任意相关的对象; 例如，可以对特定应用的所有pod，服务， 复制控制器和 部署配置进行分组。

标签是简单的键/值对，如以下示例所示：

labels:
  key1: value1
  key2: value2

考虑：

一个由`nginx` Docker容器组成的pod ，标签为 `role = webserver`。

由`Apache httpd Docker`容器组成的pod ，具有相同的`label = webserver`标签 。

定义为使用具有`role = webserver`标签的pod的服务或复制控制器 将这两个pod视为同一组的一部分。

**Projects and Users**

与OpenShift的交互与用户相关联。OpenShift用户对象表示可以通过向他们或他们的组添加角色来授予系统权限的actor 。

可以存在几种类型的用户：

一个-+------------|
|  | |
|-----------------|:-----------|
| Regular users   | 这是大多数交互式OpenShift用户将被表示的方式。常规用户在首次登录时会自动在系统中创建，也可以通过API创建。普通用户用`User`对象表示。例子：j`oe alice` |
| System users      |   当定义基础设施时，其中许多是自动创建的，主要是为了使基础架构能够安全地与API进行交互。它们包括集群管理员（可访问所有内容），每节点用户，路由器和registry使用的用户以及各种其他用户。最后，有一个`anonymous` 系统用户默认使用未认证的请求。例子： `system:admin system:openshift-registry system:node:node1.example.com`  |
| Service accounts       |    这些是与项目相关的特殊系统用户; 一些是在项目首次创建时自动创建的，而项目管理员可以创建更多，以便定义对每个项目的内容的访问。服务帐户用`ServiceAccount`对象表示。例子： `system:serviceaccount:default:deployer system:serviceaccount:foo:builder`    | 
|-----------------+------------|

每个用户必须以某种方式进行[身份验证][20]才能访问OpenShift。没有认证或无效认证的API请求被认证为`anonymous`系统用户的请求。一旦经过身份验证，策略将决定用户[授权][21]做什么。

**Namespaces**

Kubernetes命名空间提供了一种机制来对集群中的资源进行整理。在OpenShift中，项目是具有附加注释的Kubernetes命名空间。

命名空间为

 - 命名资源以避免基本的命名冲突。
 - 向受信任的用户授予管理权限。
 - 限制社区资源消耗的能力。

系统中的大多数对象都受到命名空间的限制，但有些则被排除，并且没有命名空间，包括节点和用户。

该 [Kubernetes文件][22]对命名空间的更多信息。

**Projects**

一个项目是具有附加注释的Kubernetes命名空间，是管理常规用户访问资源的中心工具。一个项目允许用户群体与其他社区隔离组织和管理他们的内容。用户必须被管理员访问项目，或者如果允许创建项目，则可以自动访问自己的项目。

 - 项目可以有一个独立的name，displayName和description。
 - 强制性name是项目的唯一标识符，并且在使用CLI工具或API时最为可见。最大名称长度为63个字符。
 - 可选项displayName是如何在Web控制台中显示项目（默认为name）。
 - 可选项description可以是项目的更详细的描述，也可以在Web控制台中看到。

每个项目都有自己的一套：

|-----------------+------------|
|  | |
|-----------------|:-----------|
| Objects   | Pod，服务，复制控制器等 |
| Policies      |   用户可以或不能对对象执行操作的规则。 |
| Constraints       |    可以限制每种物体的配额。   | 
| Service accounts       |  服务帐户会自动对项目中对​​象的指定访问进行操作。   | 
|-----------------+------------|

群集管理员可以创建项目 并将 项目的https://docs.openshift.com/enterprise/3.0/admin_guide/manage_authorization_policy.html#managing-role-bindings给用户社区的任何成员。集群管理员还可以允许开发人员创建 自己的项目。

开发人员和管理员可以使用[CLI][23]或 Web控制台与项目进行交互。

**Builds and Image Streams**

**Builds**

一个构建是变换输入参数到所得对象的过程。通常，该过程用于将输入参数或源代码转换为可运行Image。一个 BuildConfig对象是整个构建过程的定义。

OpenShift通过从构建Image创建Docker容器并将其推送到Docker registry来充分利用Kubernetes 。

构建对象共享共同特征：构建的输入，完成构建过程的需要，记录构建过程，从成功构建发布资源以及发布构建的最终状态。构建利用资源限制，指定资源的限制，例如CPU使用率，内存使用量和构建或pod执行时间。

OpenShift构建系统为 基于构建API中指定的可选类型的构建策略提供可扩展的支持。有三种可用的构建策略：

 - [Docker build][24]
 - [Source-to-Image (S2I) build][25]
 - [Custom build][26]

默认情况下，Docker构建并支持S2I构建。

生成的结果对象取决于用于创建它的构建器。对于Docker和S2I构建，生成的对象是可运行Image。对于自定义构建，生成的对象是构建器Image作者指定的任何内容。

有关构建命令的列表，请参阅[开发人员指南][27]。

有关OpenShift如何利用Docker进行构建的更多信息，请参阅[上游文档][28]。

**Docker Build**

Docker构建策略调用了普通的 docker构建命令，因此它期望具有Dockerfile和所有必需工件的存储库以生成可运行Image。

**Source-to-Image (S2I) Build**

Source-to-Image（S2I）是用于构建可重复的DockerImage的工具。它通过将应用程序源注入DockerImage并组装新的DockerImage来生成即可运行的Image。新Image包含基本Image（构建器）和内置源代码，并可随docker run命令使用。S2I支持增量版本，重新使用以前下载的依赖项，以前构建的工件等。

S2I的优点包括：

 - Image灵活性

可以编写S2I脚本，将应用程序代码注入几乎任何现有的DockerImage，利用现有的生态系统。请注意，目前，S2I依赖于tar注入应用程序源，因此Image需要能够处理tarred内容。

 - 速度

	
使用S2I，组装过程可以执行大量复杂操作，而不会在每个步骤中创建新层，从而实现快速过程。此外，可以写入S2I脚本以重新使用存储在应用程序Image的先前版本中的工件，而不必在每次运行构建时下载或构建它们。

 - Patchability

	
如果由于安全问题，底层Image需要修补程序，S2I允许您一致地重建应用程序。

 - 运营效率

	
通过限制构建操作，而不是像Dockerfile一样允许任意操作，PaaS操作员可以避免意外或故意滥用构建系统。

 - 操作安全

	
构建任意的Docker文件将主机系统暴露给root权限升级。这可以被恶意用户利用，因为整个Docker构建过程作为具有Docker特权的用户运行。S2I限制作为root用户执行的操作，并可以以非root用户身份运行脚本。

 - 用户效率

	
S2I yum install在应用程序构建期间阻止开发人员执行任意类型的操作，这可能会减慢开发迭代速度。

 - 生态系统

	
S2I鼓励Image共享生态系统，您可以利用应用程序的最佳实践。

**Custom Build**

自定义构建策略允许开发人员定义负责整个构建过程的特定构建器Image。使用您自己的构建器Image，可以自定义构建过程。

所述自定义Image助洗剂是具有嵌入的构建过程的逻辑，如建筑物的RPM或建筑物基础多克尔Image的普通Image泊坞。该 openshift /产地-自定义码头工人建设者 Image默认情况下使用。

**Image Streams**

一个Image流可以被用于自动地执行一个动作，例如更新的部署中，一个新的Image，诸如，其设置在部署中使用的基本Image的新版本，在创建时。

Image流包括由标签识别的一个或多个 [DockerImage][29]。它提供了与DockerImage存储库类似的相关Image的单个虚拟视图，并且可能包含以下任何内容的Image：

 1. 在OpenShift的综合Docker registry中，它自己的Image库
 2. 其他Image流
 3. 来自外部registry管理机构的DockerImage存储库

OpenShift组件（如构建和部署）可以观看Image流以在添加新Image时接收通知，并通过执行构建或部署进行响应。

    {
      "kind": "ImageStream",
      "apiVersion": "v1",
      "metadata": {
        "name": "origin-ruby-sample",
        "namespace": "p1",
        "selfLink": "/osapi/v1/namesapces/p1/imageStreams/origin-ruby-sample",
        "uid": "480dfe73-f340-11e4-97b5-001c422dcd49",
        "resourceVersion": "293",
        "creationTimestamp": "2015-05-05T16:03:34Z",
        "labels": {
          "template": "application-template-stibuild"
        }
      },
      "spec": {},
      "status": {
        "dockerImageRepository": "172.30.30.129:5000/p1/origin-ruby-sample",
        "tags": [
          {
            "tag": "latest",
            "items": [
              {
                "created": "2015-05-05T16:05:47Z",
                "dockerImageReference": "172.30.30.129:5000/p1/origin-ruby-sample@sha256:4d3a646b58685449179a0c61ad4baa19a8df8ba668e0f0704b9ad16f5e16e642",
                "image": "sha256:4d3a646b58685449179a0c61ad4baa19a8df8ba668e0f0704b9ad16f5e16e642"
              }
            ]
          }
        ]
      }
    }

**Image Stream Mappings**

当集成的OpenShift DockerImagepod收到一个新的Image时，它创建`ImageStreamMapping`并发送到OpenShift。这通知OpenShiftImage的命名空间，名称，标签和Docker元数据。OpenShift使用这些信息创建一个新的Image（如果它不存在），并将Image标记到Image流中。OpenShift存储有关每个Image的完整元数据（例如，命令，入口点，环境变量等）。请注意，OpenShift中的Image是不可变的。另外请注意，最大名称长度为63个字符。

`ImageStreamMapping`下面的示例导致将Image标记为 `test/origin-ruby-sample:latest`。

    {
      "kind": "ImageStreamMapping",
      "apiVersion": "v1",
      "metadata": {
        "name": "origin-ruby-sample",
        "namespace": "test"
      },
      "image": {
        "metadata": {
          "name": "a2f15cc10423c165ca221f4a7beb1f2949fb0f5acbbc8e3a0250eb7d5593ae64"
        },
        "dockerImageReference": "172.30.17.3:5001/test/origin-ruby-sample:a2f15cc10423c165ca221f4a7beb1f2949fb0f5acbbc8e3a0250eb7d5593ae64",
        "dockerImageMetadata": {
          "kind": "DockerImage",
          "apiVersion": "1.0",
          "Id": "a2f15cc10423c165ca221f4a7beb1f2949fb0f5acbbc8e3a0250eb7d5593ae64",
          "Parent": "3bb14bfe4832874535814184c13e01527239633627cdc38f18fa186e73a6b62c",
          "Created": "2015-01-23T21:47:04Z",
          "Container": "f81db8980c62d7650683326173a361c3b09f3bc41471918b6319f7df67943b54",
          "ContainerConfig": {
            "Hostname": "f81db8980c62",
            "User": "ruby",
            "AttachStdout": true,
            "ExposedPorts": {
              "9292/tcp": {}
            },
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
              "OPENSHIFT_BUILD_NAME=4bf65438-a349-11e4-bead-001c42c44ee1",
              "OPENSHIFT_BUILD_NAMESPACE=test",
              "OPENSHIFT_BUILD_SOURCE=https://github.com/openshift/ruby-hello-world",
              "PATH=/opt/ruby/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
              "STI_SCRIPTS_URL=https://raw.githubusercontent.com/openshift/sti-ruby/master/2.0/.sti/bin",
              "APP_ROOT=.",
              "HOME=/opt/ruby"
            ],
            "Cmd": [
              "/bin/sh",
              "-c",
              "tar -C /tmp -xf - \u0026\u0026 /tmp/scripts/assemble"
            ],
            "Image": "openshift/ruby-20-centos7",
            "WorkingDir": "/opt/ruby/src"
          },
          "DockerVersion": "1.4.1-dev",
          "Config": {
            "User": "ruby",
            "ExposedPorts": {
              "9292/tcp": {}
            },
            "Env": [
              "OPENSHIFT_BUILD_NAME=4bf65438-a349-11e4-bead-001c42c44ee1",
              "OPENSHIFT_BUILD_NAMESPACE=test",
              "OPENSHIFT_BUILD_SOURCE=https://github.com/openshift/ruby-hello-world",
              "PATH=/opt/ruby/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
              "STI_SCRIPTS_URL=https://raw.githubusercontent.com/openshift/sti-ruby/master/2.0/.sti/bin",
              "APP_ROOT=.",
              "HOME=/opt/ruby"
            ],
            "Cmd": [
              "/tmp/scripts/run"
            ],
            "WorkingDir": "/opt/ruby/src"
          },
          "Architecture": "amd64",
          "Size": 11710004
        },
        "dockerImageMetadataVersion": "1.0"
      },
      "tag": "latest"
    }
    
**Referencing Images in Image Streams**

`An ImageStreamTag`用于引用或检索给定Image流和标签的Image。它的名称使用以下约定：`<image stream name>:<tag>`。

`An ImageStreamImage`用于引用或检索给定Image流和Image名称的Image。它的名称使用以下约定： `<image stream name>@<name>`。

下面的示例Image来自ruby Image流，并通过询`问ImageStreamImage`名称为 ruby @ 371829c来检索：

    {
        "kind": "ImageStreamImage",
        "apiVersion": "v1",
        "metadata": {
            "name": "ruby@371829c",
            "uid": "a48b40d7-18e2-11e5-9ba2-001c422dcd49",
            "resourceVersion": "1888",
            "creationTimestamp": "2015-06-22T13:29:00Z"
        },
        "image": {
            "metadata": {
                "name": "371829c6d5cf05924db2ab21ed79dd0937986a817c7940b00cec40616e9b12eb",
                "uid": "a48b40d7-18e2-11e5-9ba2-001c422dcd49",
                "resourceVersion": "1888",
                "creationTimestamp": "2015-06-22T13:29:00Z"
            },
            "dockerImageReference": "openshift/ruby-20-centos7:latest",
            "dockerImageMetadata": {
                "kind": "DockerImage",
                "apiVersion": "1.0",
                "Id": "371829c6d5cf05924db2ab21ed79dd0937986a817c7940b00cec40616e9b12eb",
                "Parent": "8c7059377eaf86bc913e915f064c073ff45552e8921ceeb1a3b7cbf9215ecb66",
                "Created": "2015-06-20T23:02:23Z",
                "ContainerConfig": {},
                "DockerVersion": "1.6.0",
                "Author": "Jakub Hadvig \u003cjhadvig@redhat.com\u003e",
                "Config": {
                    "User": "1001",
                    "ExposedPorts": {
                        "8080/tcp": {}
                    },
                    "Env": [
                        "PATH=/opt/openshift/src/bin:/opt/openshift/bin:/usr/local/sti:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                        "STI_SCRIPTS_URL=image:///usr/local/sti",
                        "HOME=/opt/openshift/src",
                        "BASH_ENV=/opt/openshift/etc/scl_enable",
                        "ENV=/opt/openshift/etc/scl_enable",
                        "PROMPT_COMMAND=. /opt/openshift/etc/scl_enable",
                        "RUBY_VERSION=2.0"
                    ],
                    "Cmd": [
                        "usage"
                    ],
                    "Image": "8c7059377eaf86bc913e915f064c073ff45552e8921ceeb1a3b7cbf9215ecb66",
                    "WorkingDir": "/opt/openshift/src",
                    "Labels": {
                        "io.openshift.s2i.scripts-url": "image:///usr/local/sti",
                        "k8s.io/description": "Platform for building and running Ruby 2.0 applications",
                        "k8s.io/display-name": "Ruby 2.0",
                        "openshift.io/expose-services": "8080:http",
                        "openshift.io/tags": "builder,ruby,ruby20"
                    }
                },
                "Architecture": "amd64",
                "Size": 53950504
            },
            "dockerImageMetadataVersion": "1.0"
        }
    }
    
**Image Pull Policy**

pod中的每个容器都有一个DockerImage。创建Image并将其推送到Registries后，您可以在pod中引用它。

当OpenShift创建容器时，它使用容器`imagePullPolicy` 来确定在启动容器之前是否应该拖动Image。有三个可能的值`imagePullPolicy`：

 - `Always` - 总是拉Image。
 - `IfNotPresent` - 只有在节点上不存在的情况下才能拉动Image。
 - `Never` - 永不拉扯Image。

如果`imagePullPolicy` 没有指定容器的参数，OpenShift会根据Image的标签进行设置：

 1. 如果标签是 latest，OpenShift默认`imagePullPolicy`为`Always`。
 2. 否则，OpenShift默认`imagePullPolicy`为`IfNotPresent`。
 
**Importing Tag and Image Metadata**

Image流可以配置为从外部DockerImageRegistries中的Image存储库导入标记和Image元数据。见[ImageRegistries][30]的更多细节。

**Tag Tracking**

还可以配置Image流，使得标签“跟踪”另一个。例如，您可以配置latest 标签来始终引用标签“2.0”的当前Image：

    {
      "kind": "ImageStream",
      "apiVersion": "v1",
      "metadata": {
        "name": "ruby"
      },
      "spec": {
        "tags": [
          {
            "name": "latest",
            "from": {
              "kind": "ImageStreamTag",
              "name": "2.0"
            }
          }
        ]
      }
    }
    
    $ oc tag ruby:latest ruby:2.0

**Tag Removal**

删除它可以停止跟踪标签。例如，您可以停止跟踪上面设置的latest标签：

    $ oc tag -d ruby​​：latest
    
上述命令从Image流规范中删除标记，但不从Image流状态中删除。Image流规范是用户定义的，而Image流状态反映了系统从规范中获得的信息。要从Image流中完全删除标签：

    $ oc delete istag / ruby​​：latest
    
您也可以使用以下`oc tag`命令执行相同操作：

    oc tag ruby:latest ruby:2.0
    
**Importing Images from Insecure Registries**

Image流可以配置为从使用自签名证书签名的Image存储库导入标签和Image元数据，或者使用普通HTTP而不是https进行映射。为此，请将`openshift.io/image.insecureRepository` 注释添加到`true`。此设置将在连接到Registries时绕过证书验证。

    kind: ImageStream
    apiVersion: v1
    metadata:
      name: ruby
      annotations:
        openshift.io/image.insecureRepository: "true"
      spec:
        dockerImageRepository: "my.repo.com:5000/myimage"

上述定义仅影响导入标签和Image元数据。对于要在集群中使用的Image（能够做到`docker pull`），每个节点都需要配置`--insecure-registry`标记码。有关 该主题的信息，请参阅在[主机准备][31]中安装Docker 。

### 4.3. Deployments

**Replication Controllers一个

复制控制器确保始终运行指定数量的pod的副本。如果pod出口或被删除，则副本控制器用于实例化更多到所需的数量。同样，如果运行次数比预期的要多，它会删除尽可能多的数量来匹配数字。

复制控制器的定义主要包括：

 1. 需要的副本数（可以在运行时调整）。
 2. 用于创建复制pod的pod定义。
 3. 用于识别管理的pod的选择器。

选择器只是复制控制器管理的所有pod的一组标签。所以这个标签集合包含在复制控制器实例化的pod定义中。复制控制器使用此选择器来确定pod的多少个实例已经在运行，以便根据需要进行调整。

复制控制器的工作不是基于负载或流量执行自动缩放，因为它不跟踪; 相反，这将要求其副本计数由外部自动缩放器进行调整。

复制控制器是一个核心的Kubernetes对象`ReplicationController`。该 [Kubernetes文件][32]对复制控制器的更多细节。

以下是一个示例`ReplicationController`定义，其中包含一些遗漏和注释：

    apiVersion: v1
    kind: ReplicationController
    metadata:
      name: frontend-1
    spec:
      replicas: 1  【1】
      selector:    【2】
        name: frontend
      template:    【3】
        metadata:
          labels:  【4】
            name: frontend 【5】
        spec:
          containers:
          - image: openshift/hello-openshift
            name: helloworld
            ports:
            - containerPort: 8080
              protocol: TCP
          restartPolicy: Always

 1. 要运行的pod的份数。
 2. 要运行的pod的标签选择器。
 3. 控制器创建的pod的模板。
 4. pod上的标签应包括标签选择器中的标签。
 5. 扩展任何参数后的最大名称长度为63个字符。

**Deployments and Deployment Configurations**

基于复制控制器，OpenShift通过部署概念增加了对软件开发和部署生命周期的扩展支持。在最简单的情况下，部署只是创建一个新的复制控制器，并允许它启动pod。然而，OpenShift部署还提供从现有部署的映像转换到新映像的功能，并且还可以在创建复制控制器之前或之后定义要运行的钩子。

OpenShift DeploymentConfiguration对象定义部署的以下详细信息：

 1. 一个`ReplicationController`定义的元素。
 2. 用于自动创建新部署的触发器。
 3. 部署之间转换的策略。
 4. 生命周期hooks。

每次部署被触发时，无论是手动还是自动，部署器端口都可以管理部署（包括缩放旧的复制控制器，扩展新的复制控制器，并运行挂钩）。部署pod在完成部署后保持无限期的时间，以保留其部署日志。当部署被另一部分替代时，保留先前的复制控制器，以便在需要时实现轻松回滚。

有关如何创建和与部署进行交互的详细说明，请参阅[部署][33]。

以下是一个示例`DeploymentConfiguration`定义，其中包含一些遗漏和注释：
 

     apiVersion: v1
    kind: DeploymentConfig
    metadata:
      name: frontend
    spec:
      replicas: 5
      selector:
        name: frontend
      template: { ... }
      triggers:
      - type: ConfigChange 【1】
      - imageChangeParams:
          automatic: true
          containerNames:
          - helloworld
          from:
            kind: ImageStreamTag
            name: hello-openshift:latest
        type: ImageChange  【2】
      strategy:
        type: Rolling      【3】
        

 1. 一个`ConfigChange`触发器，使一个新的部署，以创建随时复制控制器模板更改。
 2. 一个`ImageChange`触发造成新的部署中，每次的支持图像的新版本命名的图像流中可用的创建。
 3. 默认`Rolling`策略使部署之间无故障转换。

### 4.4. Routes

**概观**

OpenShift路由是通过向其提供外部可访问的主机名来暴露[服务][34]的方式 `www.example.com`。

定义的路由和其服务标识的端点可以由路由器使用，以提供允许外部客户端到达您的应用程序的命名连接。每个路由由路由名称（限制为63个字符），服务选择器和（可选）安全配置组成。

**路由器**

OpenShift管理员可以 在OpenShift群集中[部署路由器][35]，从而使[由开发人员创建][36]的路由 由外部客户端使用。OpenShift中的路由层是可插拔的，默认情况下提供和支持两个可用的[路由器插件][37]。

OpenShift路由器通过将区分信息直接传递到路由器的协议为服务提供外部主机名映射和负载平衡; 主机名必须存在于协议中才能使路由器确定发送到哪里。

路由器插件假定它们可以绑定到主机端口80和443.这是为了允许外部流量路由到主机，然后通过路由器。路由器还假定网络配置为使得它可以访问集群中的所有pod。

路由器支持以下协议：

 - HTTP
 - HTTPS (with SNI)
 - WebSockets
 - TLS with SNI

WebSocket流量使用相同的路由约定，并支持与其他流量相同的TLS终止类型。
路由器使用服务选择器来查找服务和支持服务的端点。服务提供的负载平衡被绕过并被路由器自己的负载平衡替代。路由器观察集群API，并根据API对象中的任何相关更改自动更新自己的配置。路由器可以是集装箱式的或虚拟的。可以部署自定义路由器来将API对象的修改传达给外部路由解决方案。

为了首先到达路由器，主机名称的请求必须通过DNS解析为路由器或一组路由器。建议的方法是定义具有通配符DNS条目的云域，指向由指定节点上的多个路由器实例支持的虚拟IP。 [路由器VIP配置在“管理指南”][38]中有描述。需要为云域外的地址进行DNS配置。其他方法也许是可行的。

**模板路由器**

一个模板路由器是一种路由器，其提供某些基础设施信息底层路由器实现中，如：

 - 一个包装器，用于监视端点和路由。
 - 端点和路由数据，保存到消耗品表单中。
 - 将内部状态传递给可配置的模板并执行模板。
 - 调用重新加载脚本。

**可用的路由器插件**

OpenShift中提供并支持以下路由器插件。有关部署路由器的说明，请参见[“ 部署路由器”][39]。

**HAProxy模板路由器**

HAProxy模板路由器实现是模板路由器插件的参考实现。它使用 `openshift3 / ose-haproxy-router` 存储库在模板路由器插件旁边运行HAProxy实例。

下图说明了数据如何从主机通过插件流入HAProxy配置：

![此处输入图片的描述][40]

**Sticky Sessions**

实施Sticky会话取决于底层的路由器配置。默认的HAProxy模板使用`balance source` 基于源IP平衡的指令实现粘性会话。此外，模板路由器插件为底层实现提供服务名称和命名空间。这可以用于更高级的配置，例如实现在一组对等体之间同步的条形表。

该路由器实现的具体配置存储在位于 路由器容器的`/ var / lib / haproxy / conf`目录中的 `haproxy-config.template`文件中。

该`balance source`指令不区分外部客户端IP地址; 由于NAT配置，源IP地址（HAProxy remote）是一样的。除非HAProxy路由器正在运行，否则 `hostNetwork: true`所有外部客户端将被路由到一个pod。

**F5 Router**

F5路由器插件与现有的F5 BIG-IP®系统集成在您的环境中。F5 BIG-IP®版本11.4或更高版本需要F5 iControl REST API。F5路由器支持在HTTP虚拟机和请求路径上匹配的[不安全][41]， [边缘终止][42]， [重新加密终止][43]和 [直通终止][44]路由。

F5路由器在OpenShift Enterprise 2中的F5 BIG-IP®支持上具有其他功能。与早期版本中使用的OpenShift 路由守护程序进行比较时，F5路由器还支持：

 - 基于路径的路由（使用策略规则），
 - 加密连接的传递（使用解析SNI协议并使用由F5路由器维护的数据组进行服务器名称查找）的iRule实现）。

直通路由是一种特殊情况：由于F5 BIG-IP®本身没有看到HTTP请求，所以基于路径的路由在技术上是不可能的，因为它不能检查路径。同样的限制适用于模板路由器; 这是传统加密的技术限制，而不是OpenShift的技术限制。

**通过SDN路由到Pods的流量**

由于F5 BIG-IP®位于OpenShift SDN外部 ，集群管理员必须在F5 BIG-IP®和SDN上的主机（通常为OpenShift节点主机）之间创建一个对等隧道。该 斜坡节点可以配置为不可 预约 的pod，以便除了充当F5 BIG-IP®主机的网关外，它不会执行任何操作 。也可以配置多个这样的主机，并使用OpenShift ipfailover功能进行冗余; 然后，F5 BIG-IP®主机需要配置为对其隧道的远程端点使用ipfailover VIP。

**F5集成细节**

F5路由器的操作与早期版本中使用的OpenShift 路由守护程序的操作类似 。两者都使用REST API调用：

 - 创建和删除池，
 - 添加端点并从这些池中删除它们，以及
 - 配置策略规则，根据vhost进行路由到池。

两者还使用`scp`和`ssh`命令将自定义TLS / SSL证书上传到 F5 BIG-IP®。

F5路由器在虚拟服务器上配置池和策略规则，如下所示：

 - 当用户在OpenShift上创建或删除路由时，路由器为路由器创建一个到F5
   BIG-IP®的池（如果没有池已存在），并向相应的vserver的策略添加规则或从中删除规则：用于非TLS路由的HTTP
   vserver，或用于边缘或重新加密路由的HTTPS
   vserver。在边缘和重新加密路由的情况下，路由器还上传和配置TLS证书和密钥。路由器支持基于主机和路由的路由。

直通路由是一种特殊情况：为了支持那些，需要编写一个解析SNI ClientHello握手记录的iRule，并在F5数据组中查找服务器名称。路由器创建此iRule，将iRule与vserver相关联，并在创建和删除passthrough路由时更新F5数据组。除了这个实现细节之外，通过路由的工作方式与其他路由一样。

 - 当用户在OpenShift上创建服务时，路由器会将一个池添加到F5
   BIG-IP®（如果没有池已存在）。由于创建和删除该服务的端点，路由器会添加和删除相应的池成员。
 - 当用户删除与特定池相关联的路由和所有端点时，路由器将删除该池。

**路由主机名**

为了将服务暴露在外部，OpenShift路由允许您将服务与外部可访问的主机名相关联。然后，此边缘主机名用于将流量路由到服务。

当两条路线要求相同的主机时，最老的路由胜出。如果在同一命名空间中定义了具有不同路径字段的其他路由，则将添加这些路径。如果使用多个路径相同的路由，则最旧的路由优先。

    apiVersion: v1
    kind: Route
    metadata:
      name: host-route
    spec:
      host: www.example.com  【1】
      to:
        kind: Service
        name: service-name

 1. 指定用于公开服务的外部可访问的主机名。

    apiVersion: v1
    kind: Route
    metadata:
      name: no-route-hostname
    spec:
      to:
        kind: Service
        name: service-name
        
如果主机名没有作为路由定义的一部分提供，那么OpenShift会为您自动生成一个。生成的主机名的格式如下：

    <route-name>[-<namespace>].<suffix>
    
以下示例显示OpenShift生成的主机名，用于上述配置的路由，而不将主机添加到命名空间mynamespace：

    no-route-hostname-mynamespace.router.default.svc.cluster.local 【1】
    
    

 1. 生成的主机名后缀是默认路由子域 router.default.svc.cluster.local。
集群管理员还可以[自定义用作][45]其环境的[默认路由子域的后缀][46]。

**Route Types**

路由可以是安全的或不安全的。安全路由提供了使用多种类型的TLS终止来为客户端提供证书的能力。路由器支持边缘， 传播和 重新加密终止。

    apiVersion: v1
    kind: Route
    metadata:
      name: route-unsecured
    spec:
      host: www.example.com
      to:
        kind: Service
        name: service-name
        
不安全的路由是最简单的配置，因为它们不需要密钥或证书，但安全路由为连接保持私有安全性。

安全路由是指定路由的TLS终止的路由。可用的终端类型如下所述。

**基于路径的路由**

基于路径的路由指定可以与URL进行比较的路径组件（这要求路由的流量基于HTTP），以便可以使用相同的主机名（每个具有不同的路径）来提供多个路由。路由器应该根据最特定的路径匹配路由; 然而，这取决于路由器的实现。下表显示路由示例及其可访问性：

|-----------------+------------+------------|
| Route      | When Compared to   | Accessible   |
|-----------------|:-----------|:-----------|
| www.example.com/test   | www.example.com/test（www.example.com） | 	
Yes（No） |
|www.example.com/test和www.example.com    | www.example.com/test（www.example.com） | Yes（Yes） |
| www.example.com   |www.example.com/test(www.example.com) | 	
Yes (Matched by the host, not the route)(Yes) |
|-----------------+------------|
 

     apiVersion: v1
    kind: Route
    metadata:
      name: route-unsecured
    spec:
      host: www.example.com
      path: "/test"   【1】
      to:
        kind: Service
        name: service-name

 1. 路径是基于路径的路由的唯一添加属性。

基于路径的路由在使用传递TLS时不可用，因为在这种情况下路由器不会终止TLS，并且无法读取请求的内容。

**Secured Routes**

安全路由指定路由的TLS终止，并且可选地提供密钥和证书。

OpenShift中的TLS终止依赖于 SNI来提供自定义证书。在端口443处接收到的任何非SNI流量都使用TLS终止和默认证书（可能与请求的主机名不匹配，导致验证错误）进行处理。
安全路由可以使用以下三种类型的安全TLS终止。

边缘终止

在边缘终止之前，在将流量代理到其目的地之前，路由器发生TLS终止。TLS证书由路由器的前端提供，因此必须配置为路由，否则 路由器的默认证书将用于TLS终止。

    apiVersion: v1
    kind: Route
    metadata:
      name: route-edge-secured 
    spec:
      host: www.example.com
      to:
        kind: Service
        name: service-name 【1】
      tls:
        termination: edge        【2】    
        key: |-                      【3】
          -----BEGIN PRIVATE KEY-----
          [...]
          -----END PRIVATE KEY-----
        certificate: |-              【4】
          -----BEGIN CERTIFICATE-----
          [...]
          -----END CERTIFICATE-----
        caCertificate: |-            【5】
          -----BEGIN CERTIFICATE-----
          [...]
          -----END CERTIFICATE-----

 

 1. 对象的名称，限制为63个字符。
 2. 该termination字段edge用于边缘终止。
 3. 该key字段是PEM格式的密钥文件的内容。
 4. 该certificate字段是PEM格式证书文件的内容。
 5. 可能需要可选的CA证书来建立验证链。
 
由于TLS在路由器终止，因此通过内部网络从路由器到端点的连接不加密。

**Edge Termination**

通过传输终止，加密流量直接发送到目的地，而路由器不提供TLS终止。因此，不需要密钥或证书。

    apiVersion: v1
    kind: Route
    metadata:
      name: route-passthrough-secured 【1】
    spec:
      host: www.example.com
      to:
        kind: Service
        name: service-name 【1】
      tls:
        termination: passthrough     【2】
        

 1. 对象的名称，限制为63个字符。
 2. 该termination字段设置为passthrough。不需要其他加密字段。

目标端口负责为端点处的流量提供证书。这是目前唯一可以支持需要客户端证书（也称为双向认证）的方法。

**Re-encryption Termination**

重新加密是边缘终止的一种变体，其中路由器使用证书终止TLS，然后重新加密其与可能具有不同证书的端点的连接。因此，即使通过内部网络，连接的完整路径也被加密。路由器使用健康检查来确定主机的真实性。

    apiVersion: v1
    kind: Route
    metadata:
      name: route-pt-secured 【1】
    spec:
      host: www.example.com
      to:
        kind: Service
        name: service-name 【1】
      tls:
        termination: reencrypt        【2】
        key: [as in edge termination]
        certificate: [as in edge termination]
        caCertificate: [as in edge termination]
        destinationCACertificate: |-  【3】
          -----BEGIN CERTIFICATE-----
          [...]
          -----END CERTIFICATE-----

 1. 对象的名称，限制为63个字符。
 2. 该termination字段设置为reencrypt。其他领域与边缘终止相同。
 3. 该destinationCACertificate字段指定CA证书以验证端点证书，保护从路由器到目的地的连接。此字段是必需的，但仅用于重新加密。


### 4.5. Templates

**概观**

模板描述了一组对象，可以进行参数化和处理，以产生OpenShift创建的对象列表。要创建的对象可以包括用户有权在项目中创建的任何内容，例如服务， 构建配置和 部署配置。模板还可以定义一组 标签，以应用于模板中定义的每个对象。
 

     apiVersion: v1
    kind: Template
    metadata:
      name: redis-template 【1】
      annotations:
        description: "Description" 【2】
        iconClass: "icon-redis" 【3】
        tags: "database,nosql" 【4】
    objects:   【5】
    - apiVersion: v1
      kind: Pod
      metadata:
        name: redis-master
      spec:
        containers:
        - env:
          - name: REDIS_PASSWORD
            value: ${REDIS_PASSWORD} 【6】
          image: dockerfile/redis
          name: master
          ports:
          - containerPort: 6379
            protocol: TCP
    parameters:  【7】
    - description: Password used for Redis authentication
      from: '[A-Z0-9]{8}'   【8】
      generate: expression
      name: REDIS_PASSWORD
    labels:   【9】   
      redis: master

 1. 模板的名称
 2. 模板的可选描述
 3. 该模板的UI中将显示的图标;在Web控制台源中定义的CSS类的名称（搜索内容为“openshift-logos-icon”）。
 4. 该模板在UI中将具有的任意标签列表
 5. 模板将创建的对象列表（在这种情况下，单个pod）
 6. 在处理过程中将被替代的参数值
 7. 模板的参数列表
 8. 用于生成随机密码（如果未指定）的表达式
 9. 要应用于创建的所有对象的标签列表

一个模板描述了一组要在一起创建的相关对象定义，以及一组这些对象的参数。例如，应用程序可能包含由数据库支持的前端Web应用程序; 每个都由一个服务对象和部署配置对象组成，它们共享一组凭证（参数），用于前端对后端进行身份验证。可以处理模板，指定参数或允许自动生成模板（例如，唯一的DB密码），以便将模板中的对象列表实例化为内聚应用程序。

可以从文件中的定义或现有的OpenShift API对象处理模板。群集管理员可以 在API中定义可供所有用户处理的标准模板，而用户可以在自己的项目中定义自己的模板。

管理员和开发人员可以使用CLI和Web控制台与模板交互。

**Parameters**

模板允许您定义带有值的参数。该值随后被引用参与的任何地方被替换。引用可以在对象列表字段中的任何文本字段中定义。

每个参数描述一个变量和可以在objects列表字段的任何文本字段中引用的变量值。在处理过程中，该值可以被明确设置，也可以由OpenShift生成。

可以使用以下`value`字段将显式值设置为默认值：

    parameters:
      - name: USERNAME
        description: "The user name for Joe"
        value: joe
     
该`generate`字段可以设置为'expression'来指定生成的值。该from字段应使用伪正则表达式语法指定生成值的模式：

    parameters:
      - name: PASSWORD
        description: "The random user password"
        generate: expression
        from: "[a-zA-Z0-9]{12}"

 
 在上面的示例中，处理将生成一个12个字符的随机密码，包括所有大写和小写的字母和数字。

可用的语法不是完整的正则表达式语法。但是，您可以使用\w，\d和\a修饰：

 - [\w]{10}产生10个字母，数字和下划线。这符合PCRE标准，等于[a-zA-Z0-9_]{10}。
 - [\d]{10}产生10个数字。这等于[0-9]{10}。
 - [\a]{10}产生10个字母字符。这等于 [a-zA-Z]{10}。

 ### 4.6. 附加概念
 
 [更多信息点击此处][47]

5.OpenShift使用
--------------------------------------------------

 ### 5.1. OpenShift安装
 
[官方网址][51]

进入官方网址创建一个账号免费申请OpenShift空间：

![此处输入图片的描述][52]

这个是我新申请的空间，在美国西部，内存和硬盘都是1G的大小。接下来进入OpenShift的控制台，支持很多应用，可以直接创建。

![此处输入图片的描述][53]

目前免费只支持2个应用

 ### 5.2. 创建应用

通过docker image部署：

[docker镜像资源库][54]

通过Browse Catalog选择PHP再选择CakePHP + MySQL (Persistent)，一直下一步，就可以看到OpenShift帮你创建PHP和Mysql应用了，当然这个时候会提示你配额已满，项目的Pod运行成功后，下载控制台管理工具[OC][55]，解压后可以通过Windows的CMD命令行进行登陆查看项目状态。
登陆Openshift：

    C:\Users\dinghuang\Desktop>oc login https://api.starter-us-west-2.openshift.com
    Authentication required for https://api.starter-us-west-2.openshift.com:443 (openshift)
    Username: a742041978
    Password:
    Login successful.
    
    You have one project on this server: "dinghuang-test"
    
    Using project "dinghuang-test".

查看项目：

    C:\Users\dinghuang\Desktop>oc project
    Using project "dinghuang-test" on server "https://api.starter-us-west-2.openshift.com:443".

查看状态：

    C:\Users\dinghuang\Desktop>oc status
    In project TestProject (dinghuang-test) on server https://api.starter-us-west-2.openshift.com:443
    
    http://cakephp-mysql-persistent-dinghuang-test.7e14.starter-us-west-2.openshiftapps.com (svc/cakephp-mysql-persistent)
      dc/cakephp-mysql-persistent deploys istag/cakephp-mysql-persistent:latest <-
        bc/cakephp-mysql-persistent source builds https://github.com/openshift/cakephp-ex.git on openshift/php:7.0
        deployment #1 pending 18 minutes ago
    
    svc/mysql - 172.30.5.107:3306
      dc/mysql deploys openshift/mysql:5.7
        deployment #1 pending 21 minutes ago
    
    View details with 'oc describe <resource>/<name>' or list everything with 'oc get all'.
    
 ### 5.3. Configuring Automated Builds（配置自动构建）

**webhook**

[Webhook][56]，也就是人们常说的钩子，是一个很有用的工具。你可以通过定制 Webhook 来监测你在 Github.com 上的各种事件，最常见的莫过于 push 事件。如果你设置了一个监测 push 事件的 Webhook，那么每当你的这个项目有了任何提交，这个 Webhook 都会被触发，这时 Github 就会发送一个 HTTP POST 请求到你配置好的地址。

如此一来，你就可以通过这种方式去自动完成一些重复性工作；比如，你可以用 Webhook 来自动触发一些持续集成（CI）工具的运作，比如 Travis CI；又或者是通过 Webhook 去部署你的线上服务器。

Github 开发者平台的文档中对 Webhook 的所能做的事是这样描述的：

    You’re only limited by your imagination.
    
[如何实现Webhook自动部署][57]

从OpenShift GitHub存储库中分配了此应用程序的源代码 。因此，只要将代码更改推送到分支存储库，就可以使用webhook自动触发应用程序的重建。
为应用程序设置一个webhook：

 1. 从Web Console导航到包含应用程序的项目。
 2. 单击 **Browse** 选项卡，然后单击 **Builds** 。
 3. 点击复制按钮复制你的GitHub webhook。
 4. 浏览到GitHub上的分支存储库，然后单击 **Settings** 。
 5. 单击 **Webhooks & Services** 。
 6. 单击 **Add webhook* 。
 7. 将您的Webhook URL粘贴到 **Payload URL** 字段中。
 8. 在 **Secret** 字段中，键入在创建应用程序时选择的秘密。
 9. 单击 **Add webhook** 以保存。

GitHub现在尝试ping OpenShift服务器以确保通信成功。如果配置正确，您将在新的webhook URL旁边看到一个绿色的复选标记。将鼠标悬停在复选标记上，以查看上一次交货的状态。

下一次将代码更改推送到您的分支存储库时，您的应用程序将自动重建。

提交git中的更改，并将更改推到您的fork。
如果您的Webhook配置正确，您的应用程序将根据您的更改立即重建。
使用网络浏览器查看您的应用程序以查看您的更改。
现在你需要做的只是推动代码更新，而OpenShift处理其余部分。

如果您的Webhook不工作，或者如果构建失败，并且您不想在重新启动构建之前更改代码，则可能会发现手动重建Image很有用。
要根据您最近提交的更改到您的分支存储库手动重建Image：

 1. 单击 **Browse ** ，然后单击 **Builds** 。
 2. 找到您的构建，然后单击 **Rebuild** 。

6.管理员手册
--------------------------------------------------

[先决条件][58]

[快速安装][59]


  [1]: https://www.freehao123.com/wp-content/uploads/2015/10/openshift-redhat_00.gif
  [2]: https://thumbnail0.baidupcs.com/thumbnail/b1cace8c0bc7305b39c95f8309afedaa?fid=2886441168-250528-990286738670652&time=1507716000&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-DwAyZvvzRHSdNBrw1As/aLgVL4w=&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=6581623283030945840&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video
  [3]: https://docs.openshift.com/enterprise/3.0/architecture/infrastructure_components/kubernetes_infrastructure.html#high-availability-masters
  [4]: https://thumbnail0.baidupcs.com/thumbnail/68bbbec82b4f0a18f4744e4748fc310f?fid=2886441168-250528-315198303469056&time=1507770000&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-IpQQe3CzxL/br8k/nhl28RfFMi4=&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=6596120618372658386&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video
  [5]: https://docs.openshift.com/enterprise/3.0/admin_guide/master_node_configuration.html#admin-guide-master-node-configuration
  [6]: https://docs.openshift.com/enterprise/3.0/architecture/additional_concepts/authentication.html#architecture-additional-concepts-authentication
  [7]: https://docs.openshift.com/enterprise/3.0/admin_guide/web_console_customization.html#admin-guide-web-console-customization
  [8]: http://caniuse.com/#feat=websockets
  [9]: https://thumbnail0.baidupcs.com/thumbnail/a9457627a8bbc720efe174d589b003fa?fid=2886441168-250528-190563653699593&time=1507773600&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-ATlTobYN8M0rXfv8rqDumyuyKak=&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=6596554410324728024&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video
  [10]: https://docs.openshift.com/enterprise/3.0/admin_guide/master_node_configuration.html#master-configuration-files
  [11]: https://thumbnail0.baidupcs.com/thumbnail/30a60cdb1cb8f977c27a9778f1dc6898?fid=2886441168-250528-988369116616244&time=1507773600&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-aO0qWpZ6Y/AypjYtfy7RfiZTe40=&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=6596601297488937726&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video
  [12]: http://hawt.io/
  [13]: https://thumbnail0.baidupcs.com/thumbnail/be02ce9d748cd44ecb137d57a2345666?fid=2886441168-250528-1049766820955057&time=1507773600&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-tPFuL3TX7V3C1wrgjJmqcgRxer8=&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=6596651717157639039&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video
  [14]: https://thumbnail0.baidupcs.com/thumbnail/7470d589d194fe23071d12cb027d1d65?fid=2886441168-250528-975439822375542&time=1507773600&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-dnzeI4GWItQLaxOndkv1a1lr9Lk=&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=6596702752346458616&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video
  [15]: https://access.redhat.com/articles/881893
  [16]: https://registry.hub.docker.com/
  [17]: https://thumbnail0.baidupcs.com/thumbnail/0e4b2e55f6061c49c459022bfbcfe950?fid=2886441168-250528-58406471541765&time=1507773600&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-jd5iZLu0Z5klxBNH/abuJSA67lU=&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=6596958101117099421&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video
  [18]: https://docs.openshift.com/enterprise/3.0/architecture/additional_concepts/authorization.html#security-context-constraints
  [19]: https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/user-guide/services.md
  [20]: https://docs.openshift.com/enterprise/3.0/architecture/additional_concepts/authentication.html#architecture-additional-concepts-authentication
  [21]: https://docs.openshift.com/enterprise/3.0/architecture/additional_concepts/authorization.html#architecture-additional-concepts-authorization
  [22]: https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/admin/namespaces.md
  [23]: https://docs.openshift.com/enterprise/3.0/cli_reference/index.html#cli-reference-index
  [24]: https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/builds_and_image_streams.html#docker-build
  [25]: https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/builds_and_image_streams.html#source-build
  [26]: https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/builds_and_image_streams.html#custom-build
  [27]: https://docs.openshift.com/enterprise/3.0/dev_guide/builds.html#dev-guide-builds
  [28]: https://github.com/openshift/origin/blob/master/docs/builds.md#how-it-works
  [29]: https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/containers_and_images.html#docker-images
  [30]: https://docs.openshift.com/enterprise/3.0/architecture/infrastructure_components/image_registry.html#architecture-infrastructure-components-image-registry
  [31]: https://docs.openshift.com/enterprise/3.0/install_config/install/prerequisites.html#host-preparation
  [32]: https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/user-guide/replication-controller.md
  [33]: https://docs.openshift.com/enterprise/3.0/dev_guide/deployments.html#dev-guide-deployments
  [34]: https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/pods_and_services.html#services
  [35]: https://docs.openshift.com/enterprise/3.0/install_config/install/deploy_router.html#install-config-install-deploy-router
  [36]: https://docs.openshift.com/enterprise/3.0/dev_guide/routes.html#creating-routes
  [37]: https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/routes.html#available-router-plug-ins
  [38]: https://docs.openshift.com/enterprise/3.0/admin_guide/high_availability.html#configuring-a-highly-available-routing-service
  [39]: https://docs.openshift.com/enterprise/3.0/install_config/install/deploy_router.html#install-config-install-deploy-router
  [40]: https://thumbnail0.baidupcs.com/thumbnail/4a4ff54ba2731f5148af229578659b6c?fid=2886441168-250528-557210160528195&time=1507777200&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-88YdNrK4BOivGO0FMD5Cjsxfinw=&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=6598007694911344939&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video
  [41]: https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/routes.html#route-types
  [42]: https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/routes.html#edge-termination
  [43]: %E9%87%8D%E6%96%B0%E5%8A%A0%E5%AF%86%E7%BB%88%E6%AD%A2
  [44]: %E7%9B%B4%E9%80%9A%E7%BB%88%E6%AD%A2
  [45]: https://docs.openshift.com/enterprise/3.0/install_config/install/deploy_router.html#customizing-the-default-routing-subdomain
  [46]: https://docs.openshift.com/enterprise/3.0/install_config/install/deploy_router.html#customizing-the-default-routing-subdomain
  [47]: https://docs.openshift.com/enterprise/3.0/architecture/additional_concepts/networking.html
  [48]: http://t10.baidu.com/it/u=2004791304,750367866&fm=173&s=D8243873939F45CA5A5530C60300B0B0&w=640&h=373&img.JPEG
  [49]: http://t12.baidu.com/it/u=930781652,3313383653&fm=173&s=792035721B0F714944C955CE0200E0B2&w=640&h=333&img.JPEG
  [50]: https://thumbnail0.baidupcs.com/thumbnail/0cba19cdd05fb1fad2a7e14b0914f065?fid=2886441168-250528-409790244380991&time=1507705200&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-T1RCFZ/6zofmQBziFNX7%2b3mSk34=&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=6578689715682031739&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video
  [51]: https://www.openshift.com/
  [52]: https://thumbnail0.baidupcs.com/thumbnail/4b8ea3e0dda22edc98aba8fc9d79f5a3?fid=2886441168-250528-61975029248300&time=1507705200&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-Up9Kk5T30dlTISMYxfXqz4mk1oU=&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=6578522973943218343&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video
  [53]: https://thumbnail0.baidupcs.com/thumbnail/4b39492a08d92b18c12f4ed010c85bf9?fid=2886441168-250528-439032522144420&time=1507708800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-eL2bwp46LKnFsSN6mD9jFm6bmds=&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=6579934141551847587&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video
  [54]: https://hub.docker.com
  [55]: https://mirror.openshift.com/pub/openshift-v3/clients/3.6.173.0.5/windows/oc.zip
  [56]: https://developer.github.com/webhooks/
  [57]: https://segmentfault.com/a/1190000003908244
  [58]: https://docs.openshift.com/enterprise/3.0/install_config/install/prerequisites.html
  [59]: https://docs.openshift.com/enterprise/3.0/install_config/install/quick_install.html