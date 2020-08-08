# Prometheus Deployment

### Install Helm Chart & add repo ( Used for prometheus Installation )

        $ curl -fsSL -o get_helm.sh https://raw.gitusercontent.com/helm/helm/master/scripts/get-helm-3 (helm version 2 => ../scripts/get)
        $ chmod 700 get_helm.sh
        $ ./get_helm.sh
        $ helm version
        $ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
        $ helm repo update 

### Install Prometheus
-   Default Configuration

        $ helm install [name] stable/prometheus
        
-   Custom Configuration

        1. git clone helm for custionmizing prometheus
            $ git clone https://github.com/helm/charts.git

        2. modify helm/chart/stable/prometheus/values.yaml file ( alertmanager, server, pushgateway, nodeExporter ...)
            alertmanager:
            ...
                persistentVolume:
                    enable: true # false -> not install prometheus-alertmanager
                    accessmodes:
                    - ReadWriteOnce
                    mountPath: [ Your PV Path ] # ex) /mnt/nfs/alert_pv
                    size: [ Your PV Size ] # ex) 2Gi
                    storageClass: [ Your PV storageClassName ] ex) alert_pv
                    
                service:
                    externalIPs: [ IP ] ex) 192.168.2.1 # default => []
                    servicePort: [ Port_number ] ex) 8888 # default => 80
            ...
-   Install Promehteus 

        $ helm install [name] stable/prometheus -f [file_name] ex) helm instll prometheus stable/prometheus -f helm/charts/stable/prometheus/values.yaml
        
<hr/>

# Promehteus Query

                    
             
