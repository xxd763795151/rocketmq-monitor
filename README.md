# rocketmq-monitor
拿来即用的rocketmq监控、告警配置

## 监控配置

### 配置Prometheus

prometheus.yml增加如下示例配置：

```yaml
  - job_name: 'rocketmq-exporter'
    static_configs:
    - targets: ['localhost:5557']
      labels:
        Env: '开发'
        Cluster: 'dev-rmq'
```

注意增加的两个额外标签：Env和Cluster

### 配置Grafana

将目录下的grafana.json作Dashboard导入到grafana中。

## 告警配置

### 配置Prometheus

prometheus.yml中告警规则处增加如下配置：

```yaml
rule_files:
  - "rocketmq_alert.yml"
```

将目录下的rocketmq_alert.yml和其它告警规则一样放到prometheus的安装目录。

注意，示例如下，在告警规则中，使用了前面增加的Env和Cluster两个标签，注意将规则中使用该标签的值修改为自己配置的这两个标签的值:

```yaml
  - alert: "RocketMQ集群，磁盘空间不足"
    expr: rocketmq_brokeruntime_commitlog_disk_ratio{Cluster="dev-rmq",Env="开发"} * 100 > 70
    for: 1m
    labels:
      severity: warning
    annotations:
      description: '{{$labels.Env}}环境, {{$labels.Cluster}}集群, broker: {{$labels.brokerIP}}, 磁盘可用空间即将不足预警, 当前空间使用率为：{{$value}}%'
      summary: '磁盘可用空间即将不足'
```

