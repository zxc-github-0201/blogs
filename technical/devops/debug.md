### pod 排查

- 查看 event
- 对于未关闭的 pod，describe pod
- 对于非 init 时段关闭的 pod，使用[确定 pod 失败原因](https://v1-21.docs.kubernetes.io/zh/docs/tasks/debug-application-cluster/determine-reason-pod-failure/)
- kubectl log pod-name
- 对于 init 阶段的 pod，`k log -c pod-name`

### service 排查

- [官方文档](https://v1-21.docs.kubernetes.io/zh/docs/tasks/debug-application-cluster/debug-service/)

### dns 排查

- [官方文档](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
