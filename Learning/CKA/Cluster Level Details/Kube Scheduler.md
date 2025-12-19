1. Responsible for only deciding which pod goes on which node, it doesnot actually place the pod on the node
2. We want to make sure correct pods should be scheduled to correct nodes based on resource requirements i.e Memory / CPU
3. Scheduler will first filter the nodes that doesnot have the give resource requirements mentioned by the pod ( **FILTER NODES**)
4. Whatever is left it will give a priority order to the left nodes (**RANK NODES**)
5. 