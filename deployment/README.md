### Deployment - 流れ

1. Deploymentを作成
    - `kubectl apply -f deployment.yaml`
2. Deploymentのオブジェクトが作成される
3. DeploymentコントローラーがDeploymentオブジェクトを常にチェックして、ReplicaSetオブジェクトが作成される
    - ReplicaSetが作られるReplicaSetコントローラーによりPodも作成される
4. DeploymentのImageを更新する
5. 新しいReplicaSetオブジェクトを作成し、古いReplicaSetのreplicasを減らし、新しいReplicaSetを作成しreplicasを増やし、最終的にそれぞれ0と2にする
    - replicasの変更に伴い、古いPodが削除され、新しいPodが作成される

### Deployment - ポイント

1. Deploymentは、Pod(ReplicaSet)の更新を人の代わりに行ってくれる
2. 設定は基本は、デフォルトの設定でOK
   - RollingUpdate
3. Rollout(ReplicaSetの更新)は、Podテンプレートが変更された時のみトリガーされる
4. (中級) maxUnavailable, maxSurgeなどは、アプリケーションの更新に対する要件を元に調整


**Podのイメージを更新する**
`kubectl set image deployment/nginx nginx=nginx:1.15`
もしくは、ファイル内のimageを編集した後に
`kubectl apply -f deployment.yaml`

**過去の状態にロールバックする**

履歴を確認する
`kubectl rollout history deployment.v1.apps/nginx`

```bash
kubectl rollout history deployment.v1.apps/nginx   
deployment.apps/nginx 
REVISION  CHANGE-CAUSE
3         <none>
4         <none>
```

ロールバック実行

`kubectl rollout undo deployment.v1.apps/nginx --to-revision=3`
```bash
deployment.apps/nginx rolled back
```

ReplicaSetの状態を確認

`kubectl get rs`

```bash                              
NAME               DESIRED   CURRENT   READY   AGE
nginx-587945f476   0         0         0       6m21s
nginx-7d87fd5c7d   2         2         2       7m58s
```

イメージがロールバックされていることが確認できる

`kubectl get rs nginx-7d87fd5c7d -o yaml`

