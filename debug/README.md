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