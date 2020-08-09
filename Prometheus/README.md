# Prometheus Deployment

### Install Helm Chart & add repo ( Used for prometheus Installation )
* Install Hem Chart
>   ```bash
>   $ curl -fsSL -o get_helm.sh https://raw.gitusercontent.com/helm/helm/master/scripts/get-helm-3 (helm version 2 => ../scripts/get)
>   $ chmod 700 get_helm.sh
>   $ ./get_helm.sh
>   $ helm version
>   ```
* Add repo
>   ```bash
>   $ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
>   $ helm repo update
>   ```

### Install Prometheus
* Default Configuration

>    ```bash 
>    $ helm install [name] stable/prometheus
>    ```
        
-   Custom Configuration

> 1. git clone helm for custionmizing prometheus
>   ```bash
>   $ git clone https://github.com/helm/charts.git
>   ```

> 2. modify helm/chart/stable/prometheus/values.yaml file ( alertmanager, server, pushgateway, nodeExporter ...)
>   ```bash
>   alertmanager:
>   ...
>   persistentVolume:
>       enable: true # false -> not install prometheus-alertmanager
>       accessmodes:
>       - ReadWriteOnce
>       mountPath: [ Your PV Path ] # ex) /mnt/nfs/alert_pv
>       size: [ Your PV Size ] # ex) 2Gi
>       storageClass: [ Your PV storageClassName ] ex) alert_pv
>
>   service:
>       externalIPs: [ IP ] ex) 192.168.2.1 # default => []
>       servicePort: [ Port_number ] ex) 8888 # default => 80
>   ...
>   ```
* Install Promehteus 
>   ```bash
>   $ helm install [name] stable/prometheus -f [file_name] ex) helm instll prometheus stable/prometheus -f helm/charts/stable/prometheus/values.yaml
>   ```        
<hr/>

# Promehteus Query

### Prometheus Query Using CMD
>   ```bash
>   $ curl http://[prometheus-server-ip]:[port]/api/v1/query -d query="[Query]" | jq
>   ```

### Query
> #### Container CPU Usage Query
>       * Container CPU Usage Percent Query(all node)
>             rate(container_cpu_usage_seconds_total{namespace="[namesapce]", container_name="[container_name]"}[2m]) * [limit cpu usage per container(default=>0.1)] * 100
>       * Container CPU Usage Percent Query(one of the nodes)
>              rate(container_cpu_usage_seconds_total{namespace="[namesapce]", container_name="[container_name]", instance="[node_name]"}[2m]) * [limit cpu usage per container(default=>0.1)] * 100

> #### Container Memory Usage Query
>       * Container Memory Usage Percent Query(all node)
>              avg_over_time(container_memory_working_set_bytes{namespace="[namespace]"
>       * Container Memory Usage Query
>               container_memory_usage_bytes{namespace="[namesapce]", container_name="[container_name]"}

> #### Container Network Query
>       * Container Bandwidth Query
>               avg_over_time(container_network_receive_bytes_total{namespace="[namesapce]", container_name="[container_name]"}[2m]) + agv_over_time(container_network_transmit_bytes_total{namespace="[namespace]", container_name"[container_name]"}[2m])
>       * Container Packet Loss Percent Query
>               agv_over_time(container_network_receive_packets_dropped_total{namespace="[namespace]", container_name="[container_name]"}[2m] / ( avg_over_time(container_network_receive_packets_dropped_total{namespace="[namespace]", container_name="[container_name]"}[2m]) + avg_over_time(container_network_transmit_packets_dropped_total{namespace="[namespace]", container_name="[container_name]"}[2m]) + avg_over_time(container_network_receive_packets_total{namespace="[namespace]", container_name="[container_name]"}[2m]) + agv_over_time(container_network_transmit_packets_total{namespace="[namespace]", container_name="[container_name]"}[2m]]) ) * 100

> #### Node CPU Quert
>       * Node CPU Usage Percent Query(all node)
>               sum by(instance)(rate(node_cpu_seconds_total{mode!="idle"}[2m])) / [number of cpu cores] * 100
>       * Node CPU Usage percent Query(one of the nodes)
>               sum (rate(node_cpu_seconds_total{kubernetes_node="[node_name]" , mode!="idle"}[2m] / [number of cpu cores] * 100)
>       * Node CPU Cores Count Query
>               count(node_cpu_seconds_total{kubernetes_node="[node_name]", mode="user"}) without(cpu)

> #### Node Memory Query
>       * Node Memory Usage Percent Query(all node)
>               ( node_memory_MemTotal_bytes - avg_over_time(node_memory_MemFree_bytes[2m])) / node_memory_MemTotal_bytes * 100
>       * Node Memory Usage Percent Query(one of the nodes)
>               ( node_memory_MemTotal_bytes{kubernetes_node="[node_name]"} - avg_over_time(node_memory_MemFree_bytes{kubernetes_node="[node_name]"}[2m])) / node_memory_MemTotal_bytes{kubernetes_node="[node_name]"} * 100

> #### Node Network Query
>       * Node Network Bandwith Query
>               sum by(kubernetes_node)(avg_over_time(node_network_receive_bytes_total[2m])) + sum by(instance)(avg_over_time(node_network_transmit_bytes_total[2m]))
>       * Node Packets Loss Percent Query
>               sum by(kubernetes_node)(avg_over_time(node_network_receive_drop_total[2m])+avg_over_time(node_network_transmit_drop_total[2m])) / (sum by(kubernetes_node)(avg_over_time(node_network_receive_drop_total[2m]) + avg_over_time(node_network_transmit_drop_total[2m])) + sum by(kubernetes_node) avg_over_time(node_network_receive_packets_total[2m]) + avg_over_time(node_network_transmit_packets_total[2m]))) * 100
><hr/>

### Custom Metric 

> #### Send Custom Metric to Promehteus-pushgateway using Python
> * Sameple Code
>   ```python
>   from prometheus_client import CollectorRegistry, Gauge, push_to_gateway  # module import
>   
>   def send():
>       registry = CollectorRegistry()
>       g = Gauge("Test_Metric","Test",['start_time','end_time'],registry=registry) # Gauge(arg1=Metric Name, arg2=Metric Description, arg3=Label_name_list, arg4=registry)
>       g.labels(strat_time=10,end_time=20).set(10) # Gauge.labels([label1_name]=[label1_name_value],[label2_name]=[label2_value]...).set([Metric_value])
>       push_to_gateway("http://192.168.2.1:9091",job="Test",registry=registry) # push_to_gateway(arg1=Prometheus pushgateway IP address:Port, arg2=job_name, arg3=registry)
>   ```

> #### Get Custom Metric
>   ```bash
>   $ curl http://192.168.2.1:80/api/v1/query -d query="Test_Metric" | jq # curl http or https:/[prometheus-server-ip]:[port]/api/v1/query -d query="[Custom_Metric_name]"
>   {
>       "status" : "success",
>       "data" : {
>           "resultType": "vector",
>           "result": [
>           {
>               "metric: {
>               "__name__": "Test_Metric",
>               "start_time": "10",
>               "end_time": "20",
>               "job": "Test"
>               },
>               "value": [
>                   591540175.679,
>                   "10"
>               ]
>           }
>           ]
>       }
>   }
>   ```
