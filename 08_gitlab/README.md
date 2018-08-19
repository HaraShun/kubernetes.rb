# Gitlab

* 参考にした記事  
[kubernetes-gitlab/README.md at master · lwolf/kubernetes-gitlab](https://github.com/lwolf/kubernetes-gitlab/blob/master/README.md)

## GKE

1. GKE上にk8sクラスターを作成

    ```
    $ gcloud container clusters create {CLUSTER_NAME}
    ```
2. create gitlab namespace
    ```
    $ kubectl create -f namespace/gitlab-ns.yml
    ```
3. deploy redis
    ```
    $ kubectl create -f redis/redis-svc.yml
    $ kubectl create -f redis/redis-deployment.yml
    ```
4. deploy postgres
    ```
    $ kubectl create -f db/postgresql-svc.yml
    $ kubectl create -f db/postgresql-deployment.yml
    ```

5. deploy gitlab itself
    ```
    $ kubectl create -f gitlab/gitlab-svc.yml
    $ kubectl create -f gitlab/gitlab-svc-nodeport.yml
    $ kubectl create -f gitlab/gitlab-deployment.yml
    ```

6. deploy ingress controller
    ```
    $ kubectl create -f ingress/default-backend-svc.yml
    $ kubectl create -f ingress/default-backend-deployment.yml
    $ kubectl create -f ingress/nginx-settings-configmap.yml
    $ kubectl create -f ingress/configmap.yml
    $ kubectl create -f ingress/nginx-ingress-lb.yml
    $ kubectl create -f ingress/gitlab-ingress.yml
    ```

7. describe pods
    ```
    $ kubectl get po --namespace=gitlab
    ```

7. describe serivce
    ```
    $ kubectl get svc --namespace=gitlab
    ```

9. クリーンアップ
    ```
    $ kubectl delete -f namespace/gitlab-ns.yml
    ```    

---

## Minikube
[WIP]
