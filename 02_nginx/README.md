# Nginx

## Minikube

1. VM上にk8sクラスタを作成

    ```
    $ minikube start
    $ minikube dashboard # 起動するとわかりやすい
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
    $ gcloud container clusters delete {CLUSTER_NAME}
    ```

## 参考

- [kubernetes初心者のための入門ハンズオン - Qiita](https://qiita.com/mihirat/items/ebb0833d50c882398b0f)
