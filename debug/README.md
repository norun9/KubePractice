### Podのデバッグ

`kubectl get po --show-labels`

```bash
NAME      READY   STATUS             RESTARTS   AGE   LABELS
nginx-1   0/1     ImagePullBackOff   0          11m   run=nginx
nginx-2   0/1     Pending            0          11m   run=nginx
nginx-3   1/1     Running            0          11m   run=nginx
```

`--selector`オプションでpodを絞って検索する

`kubectl get pod --selector run=nginx -A`

### Output

**wide**

`kuectl get pod -o wide` や　` kubectl get node -o wide` で追加カラムが表示される

他にも**yaml**や**json**等

**jsonpath**

欲しいフィールドのみ取得する

`kubectl get pod -o jsonpath='{.items[*].metadata.name}'`

```bash
nginx-1 nginx-2 nginx-3%
```

### PodがどのNodeで動作しているか確認

`kubectl get pod nginx-1 -o wide`

```bash
NAME      READY   STATUS             RESTARTS   AGE   IP          NODE             NOMINATED NODE   READINESS GATES
nginx-1   0/1     ImagePullBackOff   0          18m   10.1.0.40   docker-desktop   <none>           <none>
```

`kubectl describe node docker-desktop`

Node上で動いているPodが確認できる
```bash
~snip~
Non-terminated Pods:          (12 in total)
  Namespace                   Name                                      CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                      ------------  ----------  ---------------  -------------  ---
  default                     nginx-1                                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         18m
  default                     nginx-3                                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         18m
  kube-system                 coredns-7db6d8ff4d-nxq77                  100m (0%)     0 (0%)      70Mi (0%)        170Mi (2%)     23h
~snip~
```

### Podの状態を確かめる


#### 方法1
`kubectl describe pod nginx-1`

Podが起動していない原因を特定する

```bash
~snip~
Events:
  Type     Reason     Age                   From               Message
  ----     ------     ----                  ----               -------
  Normal   Scheduled  21m                   default-scheduler  Successfully assigned default/nginx-1 to docker-desktop
  Normal   Pulling    19m (x4 over 21m)     kubelet            Pulling image "wrong-nginx"
  Warning  Failed     19m (x4 over 21m)     kubelet            Failed to pull image "wrong-nginx": Error response from daemon: pull access denied for wrong-nginx, repository does not exist or may require 'docker login'
  Warning  Failed     19m (x4 over 21m)     kubelet            Error: ErrImagePull
  Warning  Failed     19m (x6 over 21m)     kubelet            Error: ImagePullBackOff
  Normal   BackOff    4m13s (x64 over 21m)  kubelet            Back-off pulling image "wrong-nginx"
```

#### 方法2

`kubectl get pod nginx-1 -o yaml`


```yaml
~snip~
containerStatuses:
  - image: wrong-nginx
    imageID: ""
    lastState: {}
    name: nginx
    ready: false
    restartCount: 0
    started: false
    state:
      waiting:
        message: Back-off pulling image "wrong-nginx"
        reason: ImagePullBackOff
~snip~
```

#### 方法3

-fはfollow
-tailは行を絞るため
`kubectl logs nginx-3 -f --tail=3`

## ServiceをDebugする

1. Serviceが正常に使用できる状態かどうか

`kubectl port-forward svc/nginx-1 8080:80`
   
それから、curl等でlocalからアクセスしてみる
もしくは、エンドポイントを確認する

`kubectl describe endpoints nginx-1`


   
2. Serviceが使用できない状態の時に原因を探る 
   - セレクターにマッチするPodがが存在するか 
   - マッチしたPodは正常に動いているかどうか 
   - Portは正しいか

