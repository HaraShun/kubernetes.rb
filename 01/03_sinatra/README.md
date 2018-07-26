# Siatra + Nginx

https://github.com/yhirano55/static-sinatra-example/

## Minikube

1. VM上にk8sクラスターを作成

    ```
    $ minikube start
    $ minikube dashboard # 起動するとわかりやすい

    # クラスターが作成できない場合は以下を実行
    $ minikube delete && rm -rf ~/.minikube
    ```

2. Deployment作成

    ```
    $ kubectl apply -f k8s/deployment.yaml
    deployment "web-server" created
    ```

2. Service作成

    ```
    $ kubectl apply -f k8s/service.yaml
    service "web-server" created
    ```

3. アプリケーションの検査と表示

    ```
    $ kubectl get services
    NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
    kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP        36s
    web-server   LoadBalancer   10.97.147.131   <pending>     80:32162/TCP   12s
    ```

4. web-serverのURLを確認する

    ```
    $ minikube service web-server --url
    http://192.168.99.100:30685
    ```

5. クリーンアップ

    ```
    $ kubectl delete deployments --all
    $ kubectl delete services --all
    $ minikube delete # 終了する場合
    ```

---

## GKE

1. GKE上にk8sクラスターを作成

    ```
    $ gcloud container clusters create {CLUSTER_NAME}
    ```

2. Deployment作成

    ```
    $ kubectl apply -f k8s/deployment.yaml
    ```

2. Service作成

    ```
    $ kubectl apply -f k8s/service.yaml
    ```

3. アプリケーションの検査と表示

    ```
    $ kubectl get services
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
    kubernetes   ClusterIP      10.7.240.1    <none>           443/TCP        35m
    web-server   LoadBalancer   10.7.241.60   35.229.159.223   80:31954/TCP   1m
    ```

4. クリーンアップ

    ```
    $ kubectl delete services --all
    $ gcloud container clusters delete {CLUSTER_NAME}

    # Target Pool が消えてなかったら削除する
    $ gcloud compute target-pools list
    NAME                              REGION           SESSION_AFFINITY  BACKUP  HEALTH_CHECKS
    a94cad7df8b4a11e88e7d42010a92012  asia-northeast1  NONE                      k8s-b3bfb4ae39736392-node

    $ gcloud compute target-pools delete {NAME}

    # Load Balancer が消えてなかったら削除する
    $ gcloud compute http-health-checks list
    NAME                       HOST  PORT   REQUEST_PATH
    k8s-b3bfb4ae39736392-node        10256  /healthz

    $ gcloud compute http-health-checks delete {NAME}
    ```

## 参考

- [kubernetes初心者のための入門ハンズオン - Qiita](https://qiita.com/mihirat/items/ebb0833d50c882398b0f)
