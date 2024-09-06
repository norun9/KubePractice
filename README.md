## kubectl

### Resourceをリスト表示: kubectl get

**kubectl get [TYPE] [NAME] [flags]**

- TYPEのところに入る代表的なもの
  - pod/pods/po, node/nodes, services/svc, ...
- よく使うオプション
  - -n, --namespaces <namespace>
    - 例: `kubectl get pod -n kube-system` (kube-systemというnamespace内のpodを表示)
  - -o, --output <output_format>
    - 例: `kubectl get pot <pod_name> -o yaml`

### Resourceの詳細を表示: kubectl describe

**kubectl describe [TYPE] [NAME_PREFIX] [flags]**

- `kubectl describe --help`
- リソース詳細を確認
- TYPEに入るものは上述同様
- よく使うオプション
  - -n, --namespace <namespace>
  - 例: kubectl describe namespace kube
    - 出力: 
      ```
      Name:         kube-node-lease
      Labels:       kubernetes.io/metadata.name=kube-node-lease
      ...
      ```
- 使うシチュエーション
  - ある特定のオブジェクトに絞って使用することが多い
  - Podが動いていない場合など、原因を調査するとき: `kubectl describe pod <pod_name>`

### Resource作成/削除: kubectl create/delete

`kubectl create/delete -f FILENAME`
- リソースの作成
  - 例: `kubectl create -f pod-test.yaml`
- 作成したリソースの確認
  - 例: `kubectl get -f pod-test.yaml`
- 作成したリソースの削除
  - 例: `kubectl delete -f pod-test.yaml`
