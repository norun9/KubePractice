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