kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                                 STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
blog-data-pv                               5Gi        RWO            Retain           Bound       default/blog-data-pvc                 manual         <unset>                          66m
cache-pv                                   2Gi        RWO            Delete           Bound       techblog/cache-storage-pvc            fast-storage   <unset>                          18m
data-pv-01                                 5Gi        RWO            Retain           Available                                                        <unset>                          32h
pvc-00eb576c-4b4e-4a49-b08f-c8372fe436ad   5Gi        RWO            Delete           Bound       default/data-mysql-0                  standard       <unset>                          19h
pvc-49041e27-96c6-4acd-af5c-8eb00c7a6779   5Gi        RWO            Delete           Bound       default/data-mysql-1                  standard       <unset>                          19h
pvc-aaa3b7ea-65da-4090-b589-34a1e2ad67f5   3Gi        RWO            Delete           Bound       default/app-storage-claim             standard       <unset>                          32h
pvc-d013c402-8fc8-429e-85a2-a55242307f3c   8Gi        RWO            Delete           Bound       default/postgres-pvc                  standard       <unset>                          32h
pvc-f1f49fd9-b5a2-4856-a21e-cf2deb89d821   5Gi        RWO            Delete           Bound       default/data-mysql-2                  standard       <unset>                          19h
redis-pv-0                                 1Gi        RWO            Retain           Bound       techblog/redis-data-redis-cluster-0   fast-storage   <unset>                          10m
redis-pv-1                                 1Gi        RWO            Retain           Bound       techblog/redis-data-redis-cluster-1   fast-storage   <unset>                          10m
redis-pv-2                                 1Gi        RWO            Retain           Bound       techblog/redis-data-redis-cluster-2   fast-storage   <unset>                          10m
shared-pv                                  10Gi       RWO,ROX        Retain           Available                                                        <unset>                          32h
temp-pv                                    2Gi        RWO            Delete           Available                                                        <unset>                          32h
uploads-pv                                 10Gi       RWX            Retain           Bound       default/uploads-pvc                   manual         <unset>                          66m





kubectl get pvc
NAME                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
app-storage-claim   Bound    pvc-aaa3b7ea-65da-4090-b589-34a1e2ad67f5   3Gi        RWO            standard       <unset>                 32h
blog-data-pvc       Bound    blog-data-pv                               5Gi        RWO            manual         <unset>                 67m
data-mysql-0        Bound    pvc-00eb576c-4b4e-4a49-b08f-c8372fe436ad   5Gi        RWO            standard       <unset>                 19h
data-mysql-1        Bound    pvc-49041e27-96c6-4acd-af5c-8eb00c7a6779   5Gi        RWO            standard       <unset>                 19h
data-mysql-2        Bound    pvc-f1f49fd9-b5a2-4856-a21e-cf2deb89d821   5Gi        RWO            standard       <unset>                 19h
postgres-pvc        Bound    pvc-d013c402-8fc8-429e-85a2-a55242307f3c   8Gi        RWO            standard       <unset>                 32h
uploads-pvc         Bound    uploads-pv                                 10Gi       RWX            manual         <unset>                 67m





kubectl get storageclass -n techblog
NAME                 PROVISIONER                    RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
fast-storage         kubernetes.io/no-provisioner   Delete          WaitForFirstConsumer   false                  20m
standard (default)   k8s.io/minikube-hostpath       Delete          Immediate              false                  12d




 kubectl scale statefulset redis-cluster -n techblog --replicas=5

kubectl get pods -n techblog -l app=redis -w

kubectl get pvc -n techblog | grep redis-cluster
statefulset.apps/redis-cluster scaled
NAME              READY   STATUS    RESTARTS   AGE
redis-cluster-0   1/1     Running   0          16m
redis-cluster-1   1/1     Running   0          10m
redis-cluster-2   1/1     Running   0          16m
redis-cluster-3   1/1     Pending   0          50s
^Credis-data-redis-cluster-0   Bound     redis-pv-0   1Gi        RWO            fast-storage   <unset>                 17m
redis-data-redis-cluster-1   Bound     redis-pv-1   1Gi        RWO            fast-storage   <unset>                 16m
redis-data-redis-cluster-2   Bound     redis-pv-2   1Gi        RWO            fast-storage   <unset>                 16m
redis-data-redis-cluster-3   Pending                                          fast-storage   <unset>                 25s






kubectl exec -it redis-cluster-0 -- redis-cli info persistence
kubectl logs statefulset/redis-cluster
kubectl get events --sort-by='.lastTimestamp'
Error from server (NotFound): pods "redis-cluster-0" not found
error: error from server (NotFound): statefulsets.apps "redis-cluster" not found in namespace "default"
LAST SEEN   TYPE      REASON    OBJECT                             MESSAGE
58m         Normal    Pulling   pod/mysql-db-788bb95567-k8ztg      Pulling image "mysql:5.7"
57m         Warning   Failed    pod/mysql-db-788bb95567-k8ztg      Failed to pull image "mysql:5.7": rpc error: code = NotFound desc = failed to pull and unpack image "docker.io/library/mysql:5.7": no match for platform in manifest: not found
47m         Normal    Started   pod/storage-pod                    Container started
47m         Normal    Created   pod/storage-pod                    Container created
47m         Normal    Pulled    pod/storage-pod                    Container image "busybox:latest" already present on machine and can be accessed by the pod
36m         Normal    Created   pod/postgres-db-7d5c64f867-fbsfr   Container created
31m         Normal    Pulling   pod/multi-container                Pulling image "busybox:latest"
31m         Normal    Started   pod/multi-container                Container started
31m         Normal    Pulled    pod/multi-container                Successfully pulled image "busybox:latest" in 1.606s (2.055s including waiting). Image size: 1911327 bytes.
31m         Normal    Created   pod/multi-container                Container created
30m         Normal    Pulling   pod/env-pod                        Pulling image "busybox:latest"
30m         Normal    Pulled    pod/env-pod                        Successfully pulled image "busybox:latest" in 1.746s (1.746s including waiting). Image size: 1911327 bytes.
30m         Normal    Started   pod/env-pod                        Container started
30m         Normal    Created   pod/env-pod                        Container created
3m          Normal    BackOff   pod/mysql-db-788bb95567-k8ztg      Back-off pulling image "mysql:5.7"
3m          Warning   Failed    pod/mysql-db-788bb95567-k8ztg      Error: ImagePullBackOff
12s         Normal    Pulled    pod/postgres-db-7d5c64f867-fbsfr   Container image "postgres:13" already present on machine and can be accessed by the pod
12s         Warning   BackOff   pod/postgres-db-7d5c64f867-fbsfr   Back-off restarting failed container postgres in pod postgres-db-7d5c64f867-fbsfr_default(1544d477-1ea5-46e9-b517-48c8edfcee80)