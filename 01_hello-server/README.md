# hello-server

## Minikube

1. VM上にk8sクラスターを作成

    ```
    $ minikube start
    $ minikube dashboard # 起動するとわかりやすい
    ```

2. Deploymentを作成

    ```
    $ kubectl run hello-server --image gcr.io/google-samples/hello-app:1.0 --port 8080
    deployment "hello-server" created
    ```

3. Deploymentを公開

    ```
    $ kubectl expose deployment hello-server --type "LoadBalancer"
    service "hello-server" exposed
    ```

4. アプリケーションの検査と表示

    ```
    $ kubectl get services
    ```

    :warning: GKEの場合は、EXTERNAL-IPが表示されるが、今回はminikubeなのでpendingのまま。

5. hello-serverのURLを確認する

    ```
    $ minikube service hello-server --url
    http://192.168.99.100:32128
    ```

6. クリーンアップ

    ```
    $ kubectl delete deployments --all
    $ kubectl delete services --all
    $ minikube delete # 必要な場合
    ```

---

## GKE

1. GKE上にk8sクラスターを作成

    ```
    $ gcloud container clusters create {CLUSTER_NAME}
    ```

2. Deploymentを作成

    ```
    $ kubectl run hello-server --image gcr.io/google-samples/hello-app:1.0 --port 8080
    deployment "hello-server" created
    ```

5. Deploymentを公開する

    ```
    $ kubectl expose deployment hello-server --type "LoadBalancer"
    service "hello-server" exposed
    ```

6. アプリケーションの検査と表示

    ```
    % kubectl get services
    NAME           TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)          AGE
    hello-server   LoadBalancer   10.7.254.8   35.194.251.219   8080:32228/TCP   1m
    kubernetes     ClusterIP      10.7.240.1   <none>           443/TCP          3m
    ```

7. `http://{EXTERNAL-IP}:8080` を確認

8. クリーンアップ

    ```
    $ gcloud container clusters delete {CLUSTER_NAME}
    ```

---

## 参考

- [クイックスタート  |  Kubernetes Engine のドキュメント  |  Google Cloud](https://cloud.google.com/kubernetes-engine/docs/quickstart)
