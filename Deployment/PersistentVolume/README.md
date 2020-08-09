# k8s PV Setting

* Create yaml file for pv
>   ```bash
>   $ vi create_pv.yaml
>   
>   apiVersion: v1
>   kind: Persistent Volume
>   metadata:
>       name: [name]
>   spec:
>       storageClasssName: [storageClassName] # this name is using to PVC Connect
>       capacity:
>           storage: [storage capacity] # ex) 8Gi
>       accessModes:
>       - ReadWirteMany
>       nfs:
>           server: [NFS_Server_IP] # ex) 192.168.2.1
>           path:   [NFS_Folder_Path]   # ex) /mnt/nfs
>   ```

* apply pv in k8s
>   ```bash
>   $ kubectl apply -f create_pv.yaml
>   $ kubectl get pv
>   NAME        CAPACITY    ACCESS MODES    RECLAIM POLICY  STATUS  CLAIM                       STORAGECLASS    REASON  AGE
>   Server_pv   8Gi         RWX             Retain          Bound   defalut/promehteus-server   server                  10s
