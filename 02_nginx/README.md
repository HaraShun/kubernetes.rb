# Nginx

## Minikube

1. VM上にk8sクラスタを作成

    ```
    $ minikube start
    $ minikube dashboard # 起動するとわかりやすい
    ```

2. Deploymentを作成

    ```
    $ kubectl run nginx --image=nginx:1.11.3 --port 80
    deployment "nginx" created
    ```

3. Deploymentを公開

    ```
    $ kubectl expose deployment nginx --type LoadBalancer
    service "nginx" exposed
    ```

4. アプリケーションの検査と表示

    ```
    $ kubectl get services
    NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
    kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP        29s
    nginx        LoadBalancer   10.102.222.47   <pending>     80:30685/TCP   5s
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
    $ kubectl run nginx --image=nginx:1.11.3 --port 80
    deployment "nginx" created
    ```

3. Deploymentを公開

    ```
    $ kubectl expose deployment nginx --type LoadBalancer
    service "nginx" exposed
    ```

4. アプリケーションの検査と表示

    ```
    $ kubectl get services
    NAME         TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
    kubernetes   ClusterIP      10.7.240.1     <none>           443/TCP        20m
    nginx        LoadBalancer   10.7.243.161   35.194.251.219   80:32203/TCP   1m
    ```

5. `http://{EXTERNAL-IP}:8080` を確認

6. クリーンアップ

    ```
    $ gcloud container clusters delete {CLUSTER_NAME}
    ```

## 参考

- [kubernetes初心者のための入門ハンズオン - Qiita](https://qiita.com/mihirat/items/ebb0833d50c882398b0f)
