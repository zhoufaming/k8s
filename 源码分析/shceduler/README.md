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
NoDiskConflictPred ：pod所需的卷是否和节点已存在的卷冲突。如果节点已经挂载了某个卷，其它同样使用这个卷的pod不能再调度到这个主机上
