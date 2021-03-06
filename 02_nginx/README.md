# Nginx

## GKE

1. GKE上にクラスタを作成

    ```
    $ gcloud container clusters create {CLUSTER_NAME}
    ```

2. Deploymentを作成

    ```
    $ kubectl apply -f k8s/deployment.yaml
    deployment.apps/nginx created
    ```

3. Deploymentを公開

    ```
    $ kubectl apply -f k8s/service.yaml
    service/nginx created
    ```

4. アプリケーションの検査と表示

    ```
    $ kubectl get services
    NAME         TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)        AGE
    kubernetes   ClusterIP      10.55.240.1    <none>         443/TCP        2m
    nginx        LoadBalancer   10.55.242.15   35.200.28.96   80:31947/TCP   1m
    ```

5. `http://{EXTERNAL-IP}` を確認

6. クリーンアップ

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

---

## Minikube

    1. VM上にk8sクラスタを作成

        ```
        $ minikube start
        $ minikube dashboard # 起動するとわかりやすい

        # クラスターが作成できない場合は以下を実行
        $ minikube delete && rm -rf ~/.minikube
        ```

    2. Deploymentを作成

        ```
        $ kubectl apply -f k8s/deployment.yaml
        deployment.apps/nginx created
        ```

    3. Deploymentを公開

        ```
        $ kubectl apply -f k8s/service.yaml
        service/nginx created
        ```

    4. アプリケーションの検査と表示

        ```
        $ kubectl get services
        NAME          TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
        kubernetes    ClusterIP      10.96.0.1        <none>        443/TCP        57m
        nginx         LoadBalancer   10.103.122.40    <pending>     80:31052/TCP   13s
        ```

    5. nginxのURLを確認する

        ```
        $ minikube service nginx --url
        http://192.168.99.100:30685
        ```

    6. クリーンアップ

        ```
        $ kubectl delete deployments --all
        $ kubectl delete services --all
        $ minikube delete # 終了する場合
        ```

---

## 参考

- [kubernetes初心者のための入門ハンズオン - Qiita](https://qiita.com/mihirat/items/ebb0833d50c882398b0f)
