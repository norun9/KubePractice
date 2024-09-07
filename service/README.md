### 概要

**役割**: Podの集合を抽象化して公開
- Podの集合に対するDNS名
- Podの集合に対する負荷分散

**背景**: 各PodはIPアドレスを持っているが、Podの作成・削除の度に、アプリケーションのIPアドレスの集合が変化する → 使う側が大変
- ServiceがDNS名を提供し、後ろにあるIPアドレスの管理をしてくれる → 常に同じ名前を使ってアプリケーションを参照できる！

### 動作確認

`kubectl apply -f pod-my-app.yaml,pod-your-app.yaml`

```bash
pod/my-app created
pod/your-app created
```

`--show-labels`でPodのLABELSを確認できる

`kubectl get pod --show-labels`

```bash
NAME       READY   STATUS    RESTARTS   AGE   LABELS
my-app     1/1     Running   0          18s   app=MyApp
your-app   1/1     Running   0          18s   app=YourApp
```

`kubectl get pod --selector app=YourApp`

```bash
NAME       READY   STATUS    RESTARTS   AGE
your-app   1/1     Running   0          38s
```

Serviceをapply

`kubectl apply -f service-my-app.yaml`

Service確認

`kubectl get service`

```bash
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   3h50m
my-app       ClusterIP   10.111.138.50   <none>        80/TCP    31s
```

**エンドポイント確認**

`kubectl get endpoints my-app`

```bash
NAME     ENDPOINTS      AGE
my-app   10.1.0.33:80   92s
```

`kubectl get pod --selector app=MyApp -o wide`

```bash
NAME     READY   STATUS    RESTARTS   AGE     IP          NODE             NOMINATED NODE   READINESS GATES
my-app   1/1     Running   0          4m52s   10.1.0.33   docker-desktop   <none>           <none>
```


**http://localhost:8080/ にアクセスしてみる**

kubectl port-forward <Service>/<Service名> <ローカルのポート:Service側のポート>
`kubectl port-forward svc/my-app 8080:80`

```bash
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

`kubectl logs my-app --follow`