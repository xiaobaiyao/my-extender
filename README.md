# my-extender
在示例程序的基础上，对调度和打分机制做如下修改：  
- predicates.go:节点随机产生0-2的随机数，若随机数不为0则fit：
```
func LuckyPredicate(pod *v1.Pod, node v1.Node) (bool, []string, error) {
	// 节点随机产生0-2的随机数，若随机数不为0则fit
	lucky := rand.Intn(3) != 0
	if lucky {
		log.Printf("pod %v/%v is lucky to fit on node %v\n", pod.Name, pod.Namespace, node.Name)
		return true, nil, nil
	}
	log.Printf("pod %v/%v is unlucky to fit on node %v\n", pod.Name, pod.Namespace, node.Name)
	return false, []string{LuckyPredFailMsg}, nil
}

```
- priorities.go:试验性地对全部score加一，但对整体的调度没有任何影响
```
	for i, node := range nodes {
		//在源代码的基础上将score+1目的是验证不输出score是否是因为score值本身的问题
		score := rand.Intn(schedulerapi.MaxPriority + 1)+1
		log.Printf(luckyPrioMsg, pod.Name, pod.Namespace, score)
		hostPriorityList[i] = schedulerapi.HostPriority{
			Host:  node.Name,
			Score: score,
		}
	}
```
