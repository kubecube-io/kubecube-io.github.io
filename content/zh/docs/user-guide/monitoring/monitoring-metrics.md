---
title: "监控能力说明"
weight: 3
---

本文档介绍了 KubeCube 的监控能力。

## 总体能力说明

| 监控内容         | 采集源                               | 说明                                                         |
| ---------------- | ------------------------------------ | ------------------------------------------------------------ |
| k8s 核心组件监控 | 各个 k8s 服务组件暴露的 metrics 接口 | 监控 k8s api-server、controller-manager、kube-proxy、scheduler、etcd、coredns和kubelet组件的功能运行情况。查看指标时以组件名字开头+下划线。 |
| k8s 节点监控     | node-export                          | node-export 监控 k8s 集群 node 节点的 cpu、memory、network、disk 等信息。 |
| k8s 容器指标     | cAdvisor                             | k8s  kubelet 内置的 cAdvisor 会监控各个节点中运行的容器。    |
| k8s 资源监控     | kube-state-metrics                   | kube-state-metrics 关注各种 k8s 资源对象的指标信息，监控各项 k8s 资源包括node、deployment、pod等。 |



## 常用指标说明

### Node-exporter

Node-exporter 关注容器的指标，更多指标信息参考[ Node-exporter ](https://github.com/prometheus/node_exporter)。

| Metric name                              | Description     |
| ---------------------------------------- | --------------- |
| `instance:node_num_cpu:sum`              | 节点的CPU核数   |
| `instance:node_load1_per_cpu:ratio`      | 节点的CPU负载率 |
| `instance:node_memory_utilisation:ratio` | 节点内存使用率  |
| `node_cpu_*`                             | 节点CPU指标     |
| `node_memory_*`                          | 节点内存指标    |
| `node_disk_*`                            | 节点磁盘指标    |
| `node_network_*`                         | 节点网络指标    |

### CAdvisor

CAdcisor 关注容器的指标，更多指标信息参考[ cAdvisor ](https://github.com/google/cadvisor/blob/master/docs/storage/prometheus.md)。

| Metric name                                        | Type    | Description                                                  | Unit (where applicable) | option parameter |
| -------------------------------------------------- | ------- | ------------------------------------------------------------ | ----------------------- | ---------------- |
| `container_cpu_cfs_periods_total`                  | Counter | 容器生命周期中度过的 cpu 周期总数                            |                         | cpu              |
| `container_cpu_cfs_throttled_periods_total`        | Counter | 容器生命周期中度过的受限的 cpu 周期总数                      |                         | cpu              |
| `container_cpu_cfs_throttled_seconds_total`        | Counter | 容器 cpu 受限制的持续时间                                    | seconds                 | cpu              |
| `container_cpu_load_average_10s`                   | Gauge   | 监控过去10秒cpu负载的平均值                                  |                         | cpuLoad          |
| `container_cpu_system_seconds_total`               | Counter | 内核态累计消耗的 cpu 时间                                    | seconds                 | cpu              |
| `container_cpu_usage_seconds_total`                | Counter | 累计消耗的 cpu 时间                                          | seconds                 | cpu              |
| `container_cpu_user_seconds_total`                 | Counter | 用户态累计消耗的 cpu 时间                                    | seconds                 | cpu              |
| `container_file_descriptors`                       | Gauge   | 打开的文件描述符数                                           |                         | process          |
| `container_fs_io_current`                          | Gauge   | 当前正在进行IO操作的进程数                                   |                         | diskIO           |
| `container_fs_limit_bytes`                         | Gauge   | 容器文件系统可使用的字节数                                   | bytes                   | disk             |
| `container_fs_usage_bytes`                         | Gauge   | 容器文件系统已使用的字节数                                   | bytes                   | disk             |
| `container_memory_cache`                           | Gauge   | 内存cache字节数                                              | bytes                   | memory           |
| `container_memory_failcnt`                         | Counter | 内存达到限制值的次数                                         |                         | memory           |
| `container_memory_mapped_file`                     | Gauge   | 内存映射文件大小                                             | bytes                   | memory           |
| `container_memory_max_usage_bytes`                 | Gauge   | 记录的最大内存使用值                                         | bytes                   | memory           |
| `container_memory_usage_bytes`                     | Gauge   | 当前内存使用情况，包括所有内存                               | bytes                   | memory           |
| `container_network_receive_bytes_total`            | Counter | 容器网络接收的累积字节数                                     | bytes                   | network          |
| `container_network_receive_errors_total`           | Counter | 容器网络接收时遇到的累积错误次数                             |                         | network          |
| `container_network_receive_packets_dropped_total`  | Counter | 容器网络接收时丢掉的网络包数                                 |                         | network          |
| `container_network_receive_packets_total`          | Counter | 容器网络累积接收的网络包数                                   |                         | network          |
| `container_network_tcp_usage_total`                | Gauge   | 容器的TCP链接使用统计                                        |                         | tcp              |
| `container_network_transmit_bytes_total`           | Counter | 容器网络传输的累积字节数                                     | bytes                   | network          |
| `container_network_transmit_errors_total`          | Counter | 容器网络传输时遇到的累积错误次数                             |                         | network          |
| `container_network_transmit_packets_dropped_total` | Counter | 容器网络传输时丢掉的网络包数                                 |                         | network          |
| `container_network_transmit_packets_total`         | Counter | 容器网络传输累积的网络包数                                   |                         | network          |
| `container_processes`                              | Gauge   | 容器正在运行的进程数                                         |                         | process          |
| `container_sockets`                                | Gauge   | 容器打开的sockets链接数                                      |                         | process          |
| `container_spec_cpu_period`                        | Gauge   | 容器的CPU周期                                                |                         | -                |
| `container_spec_cpu_quota`                         | Gauge   | 容器的CPU配额                                                |                         | -                |
| `container_spec_memory_limit_bytes`                | Gauge   | 容器的内存限制                                               | bytes                   | -                |
| `container_tasks_state`                            | Gauge   | 处于这些状态的任务数 (`sleeping`, `running`, `stopped`, `uninterruptible`, or `ioawaiting`) |                         | cpuLoad          |
| `container_threads`                                | Gauge   | 容器正在运行的threads数吗                                    |                         | process          |
| `container_threads_max`                            | Gauge   | 容器允许的最大threads数码                                    |                         | process          |

### Kube-state-metrics

Kube-state-metrics 关注各种 k8s 资源对象的指标信息，详细指标说明参考 [kube-state-metrics 指标说明](https://github.com/kubernetes/kube-state-metrics/tree/v1.9.8/docs)。

#### Pod Metrics

| Metric name                                      | Metric type | Labels/tags                                                  |
| ------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| kube_pod_status_phase                            | Gauge       | `pod=<pod-name>` `namespace=<pod-namespace>` `phase=<Pending/Running/Succeeded/Failed/Unknown>` |
| kube_pod_container_info                          | Gauge       | `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` `image=<image-name>` `image_id=<image-id>` `container_id=<containerid>` |
| kube_pod_container_status_waiting_reason         | Gauge       | `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` `reason=<ContainerCreating/CrashLoopBackOff/ErrImagePull/ImagePullBackOff/CreateContainerConfigError/InvalidImageName/CreateContainerError>` |
| kube_pod_container_status_running                | Gauge       | `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` |
| kube_pod_container_status_terminated_reason      | Gauge       | `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` `reason=<OOMKilled/Error/Completed/ContainerCannotRun/DeadlineExceeded>` |
| kube_pod_container_status_ready                  | Gauge       | `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` |
| kube_pod_container_status_restarts_total         | Counter     | `container=<container-name>` `namespace=<pod-namespace>` `pod=<pod-name>` |
| kube_pod_container_resource_requests_cpu_cores   | Gauge       | `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` `node=< node-name>` |
| kube_pod_container_resource_requests             | Gauge       | `resource=<resource-name>` `unit=<resource-unit>` `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` `node=< node-name>` |
| kube_pod_container_resource_limits               | Gauge       | `resource=<resource-name>` `unit=<resource-unit>` `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` `node=< node-name>` |
| kube_pod_init_container_info                     | Gauge       | `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` `image=<image-name>` `image_id=<image-id>` `container_id=<containerid>` |
| kube_pod_init_container_status_running           | Gauge       | `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` |
| kube_pod_init_container_status_terminated_reason | Gauge       | `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` `reason=<OOMKilled/Error/Completed/ContainerCannotRun/DeadlineExceeded>` |
| kube_pod_init_container_status_ready             | Gauge       | `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` |
| kube_pod_init_container_resource_limits          | Gauge       | `resource=<resource-name>` `unit=<resource-unit>` `container=<container-name>` `pod=<pod-name>` `namespace=<pod-namespace>` `node=< node-name>` |

#### Deployment Metrics

| Metric name                                                 | Metric type | Labels/tags                                                  |
| ----------------------------------------------------------- | ----------- | ------------------------------------------------------------ |
| kube_deployment_status_replicas                             | Gauge       | `deployment=<deployment-name>` `namespace=<deployment-namespace>` |
| kube_deployment_status_replicas_available                   | Gauge       | `deployment=<deployment-name>` `namespace=<deployment-namespace>` |
| kube_deployment_status_replicas_unavailable                 | Gauge       | `deployment=<deployment-name>` `namespace=<deployment-namespace>` |
| kube_deployment_status_condition                            | Gauge       | `deployment=<deployment-name>` `namespace=<deployment-namespace>` `condition=<deployment-condition>` `status=<true/false/unknown>` |
| kube_deployment_spec_replicas                               | Gauge       | `deployment=<deployment-name>` `namespace=<deployment-namespace>` |
| kube_deployment_spec_paused                                 | Gauge       | `deployment=<deployment-name>` `namespace=<deployment-namespace>` |
| kube_deployment_spec_strategy_rollingupdate_max_unavailable | Gauge       | `deployment=<deployment-name>` `namespace=<deployment-namespace>` |
| kube_deployment_spec_strategy_rollingupdate_max_surge       | Gauge       | `deployment=<deployment-name>` `namespace=<deployment-namespace>` |

#### DaemonSet Metrics

| Metric name                                    | Metric type | Labels/tags                                                  |
| ---------------------------------------------- | ----------- | ------------------------------------------------------------ |
| kube_daemonset_status_current_number_scheduled | Gauge       | `daemonset=<daemonset-name>` `namespace=<daemonset-namespace>` |
| kube_daemonset_status_desired_number_scheduled | Gauge       | `daemonset=<daemonset-name>` `namespace=<daemonset-namespace>` |
| kube_daemonset_status_number_available         | Gauge       | `daemonset=<daemonset-name>` `namespace=<daemonset-namespace>` |
| kube_daemonset_status_number_misscheduled      | Gauge       | `daemonset=<daemonset-name>` `namespace=<daemonset-namespace>` |
| kube_daemonset_status_number_ready             | Gauge       | `daemonset=<daemonset-name>` `namespace=<daemonset-namespace>` |
| kube_daemonset_status_number_unavailable       | Gauge       | `daemonset=<daemonset-name>` `namespace=<daemonset-namespace>` |
| kube_daemonset_updated_number_scheduled        | Gauge       | `daemonset=<daemonset-name>` `namespace=<daemonset-namespace>` |

#### Stateful Set Metrics

| Metric name                              | Metric type | Labels/tags                                                  |
| ---------------------------------------- | ----------- | ------------------------------------------------------------ |
| kube_statefulset_status_replicas         | Gauge       | `statefulset=<statefulset-name>` `namespace=<statefulset-namespace>` |
| kube_statefulset_status_replicas_current | Gauge       | `statefulset=<statefulset-name>` `namespace=<statefulset-namespace>` |
| kube_statefulset_status_replicas_ready   | Gauge       | `statefulset=<statefulset-name>` `namespace=<statefulset-namespace>` |
| kube_statefulset_status_replicas_updated | Gauge       | `statefulset=<statefulset-name>` `namespace=<statefulset-namespace>` |
| kube_statefulset_replicas                | Gauge       | `statefulset=<statefulset-name>` `namespace=<statefulset-namespace>` |
| kube_statefulset_created                 | Gauge       | `statefulset=<statefulset-name>` `namespace=<statefulset-namespace>` |
| kube_statefulset_status_current_revision | Gauge       | `statefulset=<statefulset-name>` `namespace=<statefulset-namespace>` `revision=<statefulset-current-revision>` |
| kube_statefulset_status_update_revision  | Gauge       | `statefulset=<statefulset-name>` `namespace=<statefulset-namespace>` `revision=<statefulset-update-revision>` |

#### Job Metrics

| Metric name                           | Metric type | Labels/tags                                       |
| ------------------------------------- | ----------- | ------------------------------------------------- |
| kube_job_spec_parallelism             | Gauge       | `job_name=<job-name>` `namespace=<job-namespace>` |
| kube_job_spec_completions             | Gauge       | `job_name=<job-name>` `namespace=<job-namespace>` |
| kube_job_spec_active_deadline_seconds | Gauge       | `job_name=<job-name>` `namespace=<job-namespace>` |
| kube_job_status_active                | Gauge       | `job_name=<job-name>` `namespace=<job-namespace>` |
| kube_job_status_succeeded             | Gauge       | `job_name=<job-name>` `namespace=<job-namespace>` |
| kube_job_status_failed                | Gauge       | `job_name=<job-name>` `namespace=<job-namespace>` |
| kube_job_status_start_time            | Gauge       | `job_name=<job-name>` `namespace=<job-namespace>` |
| kube_job_status_completion_time       | Gauge       | `job_name=<job-name>` `namespace=<job-namespace>` |
| kube_job_complete                     | Gauge       | `job_name=<job-name>` `namespace=<job-namespace>` |
| kube_job_failed                       | Gauge       | `job_name=<job-name>` `namespace=<job-namespace>` |
| kube_job_created                      | Gauge       | `job_name=<job-name>` `namespace=<job-namespace>` |

#### CronJob Metrics

| Metric name                                 | Metric type | Labels/tags                                              |
| ------------------------------------------- | ----------- | -------------------------------------------------------- |
| kube_cronjob_next_schedule_time             | Gauge       | `cronjob=<cronjob-name>` `namespace=<cronjob-namespace>` |
| kube_cronjob_status_active                  | Gauge       | `cronjob=<cronjob-name>` `namespace=<cronjob-namespace>` |
| kube_cronjob_status_last_schedule_time      | Gauge       | `cronjob=<cronjob-name>` `namespace=<cronjob-namespace>` |
| kube_cronjob_spec_suspend                   | Gauge       | `cronjob=<cronjob-name>` `namespace=<cronjob-namespace>` |
| kube_cronjob_spec_starting_deadline_seconds | Gauge       | `cronjob=<cronjob-name>` `namespace=<cronjob-namespace>` |

#### PersistentVolume Metrics

| Metric name                          | Metric type | Labels/tags                                                  |
| ------------------------------------ | ----------- | ------------------------------------------------------------ |
| kube_persistentvolume_capacity_bytes | Gauge       | `persistentvolume=<pv-name>`                                 |
| kube_persistentvolume_status_phase   | Gauge       | `persistentvolume=<pv-name>` `phase=<Bound/Failed/Pending/Available/Released>` |
| kube_persistentvolume_info           | Gauge       | `persistentvolume=<pv-name>` `storageclass=<storageclass-name>` |

#### PersistentVolumeClaim Metrics

| Metric name                                                | Metric type | Labels/tags                                                  |
| ---------------------------------------------------------- | ----------- | ------------------------------------------------------------ |
| kube_persistentvolumeclaim_access_mode                     | Gauge       | `access_mode=<persistentvolumeclaim-access-mode>` `namespace=<persistentvolumeclaim-namespace>` `persistentvolumeclaim=<persistentvolumeclaim-name>` |
| kube_persistentvolumeclaim_info                            | Gauge       | `namespace=<persistentvolumeclaim-namespace>` `persistentvolumeclaim=<persistentvolumeclaim-name>` `storageclass=<persistentvolumeclaim-storageclassname>` `volumename=<volumename>` |
| kube_persistentvolumeclaim_resource_requests_storage_bytes | Gauge       | `namespace=<persistentvolumeclaim-namespace>` `persistentvolumeclaim=<persistentvolumeclaim-name>` |
| kube_persistentvolumeclaim_status_phase                    | Gauge       | `namespace=<persistentvolumeclaim-namespace>` `persistentvolumeclaim=<persistentvolumeclaim-name>` `phase=<Pending/Bound/Lost>` |

#### Node Metrics

| Metric name                  | Metric type | Labels/tags                                                  |
| ---------------------------- | ----------- | ------------------------------------------------------------ |
| kube_node_info               | Gauge       | `node=<node-address>` `kernel_version=<kernel-version>` `os_image=<os-image-name>` `container_runtime_version=<container-runtime-and-version-combination>` `kubelet_version=<kubelet-version>` `kubeproxy_version=<kubeproxy-version>` `pod_cidr=<pod-cidr>` `provider_id=<provider-id>` |
| kube_node_spec_taint         | Gauge       | `node=<node-address>` `key=<taint-key>` `value=<taint-value>` `effect=<taint-effect>` |
| kube_node_status_capacity    | Gauge       | `node=<node-address>` `resource=<resource-name>` `unit=<resource-unit>` |
| kube_node_status_allocatable | Gauge       | `node=<node-address>` `resource=<resource-name>` `unit=<resource-unit>` |
| kube_node_status_condition   | Gauge       | `node=<node-address>` `condition=<node-condition>` `status=<true/false/unknown>` |



