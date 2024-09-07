`kubectl logs test-pod`

`TEST_ENV=test`が適用されている
```bash
# 「env」コマンドで出力された結果が以下
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=test-pod
TEST_ENV=test
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
HOME=/root
```

### Resource削除

`kubectl delete -f configmap-env-ver.yaml`

```bash
configmap "test-config" deleted
pod "test-pod" deleted
```

### Podのコンテナに入るコマンド

Pod内のコンテナが1つの場合

`kubectl exec -it <POD_NAME> sh`

複数の場合、コンテナ名を指定する

`kubectl exec -it test-pod -c <CONTAINER_NAME> -- sh`