Kubernetes调度器的源码位于kubernetes/pkg/scheduler中
algorithm
    --predicates       //节点筛选策略
    -- priorities         //节点打分策略
        -- util
algorithmprovider
    -- defaults          //定义默认的调度器 
    
    
Scheduler创建和运行的过程  对应的代码在kubernetes/pkg/scheduler/scheduler.go
调度分为几个部分：
首先是预选过程，过滤掉不满足条件的节点，这个过程称为Predicates；
然后是优选过程，对通过的节点按照优先级排序，称之为Priorities；
最后从中选择优先级最高的节点。如果中间任何一步骤有错误，就直接返回错误。

预选（Predicates）:
NoDiskConflictPred ：pod所需的卷是否和节点已存在的卷冲突。如果节点已经挂载了某个卷，其它同样使用这个卷的pod不能再调度到这个主机上.

NoVolumeZoneConflictPred : 检查节点是否有足够资源（例如 CPU、内存与GPU等）满足一个Pod的运行需求。调度器首先会确认节点是否有足够的资源运行Pod，如果资源不能满足Pod需求，会返回失败原因（例如，CPU/内存 不足等）。这里需要注意的是：根据实际已经分配的资源量做调度，而不是使用已实际使用的资源量做调度。

PodFitsHostPortsPred ： 检查Pod容器所需的HostPort是否已被节点上其它容器或服务占用。如果所需的HostPort不满足需求，那么Pod不能调度到这个主机上。

MatchNodeSelectorPred : 检查节点标签（label）是否匹配Pod的nodeSelector属性要求。

MaxEBSVolumeCountPred : 确保已挂载的EBS存储卷不超过设置的最大值（默认值为39。Amazon推荐最大卷数量为40，其中一个卷为root卷，具体可以参考http://docs.aws.amazon.com/AWS ... imits）。调度器会检查直接使用以及间接使用这种类型存储的PVC。计算不同卷的总和，如果卷数目会超过设置的最大值，那么新Pod不能调度到这个节点上。 最大卷的数量可通过环境变量KUBE_MAX_PD_VOLS设置。

CheckNodeMemoryPressurePred : 判断节点是否已经进入到内存压力状态，如果是则只允许调度内存为0标记的Pod。检查Pod能否调度到内存有压力的节点上。如有节点存在内存压力， Guaranteed类型的Pod（例如，requests与limit均指定且值相等） 不能调度到节点上。

CheckNodeDiskPressurePred :判断节点是否已经进入到磁盘压力状态，如果是，则不调度新的Pod。

CheckNodePIDPressurePred : 判断节点是否已经进入到pid进程压力状态，如果是，则不调度新的Pod。

PodToleratesNodeTaintsPred : 根据 taints 和 toleration 的关系判断Pod是否可以调度到节点上Pod是否满足节点容忍的一些条件。

MatchInterPodAffinityPred : 节点亲和性筛选。

GeneralPred ：包含一些基本的筛选规则，主要考虑 Kubernetes 资源是否充足，比如 CPU 和 内存 是否足够，端口是否冲突、selector 是否匹配等。

优选（Priorities）：
