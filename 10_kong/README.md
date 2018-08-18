# Kong

* 参考記事
[Install - Kong on Kubernetes | Kong - Open-Source API Management and Microservice Management](https://docs.konghq.com/install/kubernetes/)


## GKE

0. Initial setup

    ```
    git clone https://github.com/Kong/kong-dist-kubernetes.git
    cd kong-dist-kubernetes
    ```

1. GKE上にk8sクラスターを作成

    ```
    $ gcloud container clusters create {CLUSTER_NAME}
    ```

2. Deploy a datastore

    Postgres の場合
    ```
    $ kubectl create -f postgres.yaml
    ```

    Cassandra の場合
    ```
    $ kubectl create -f cassandra.yaml
    ```

3. Prepare database

    Postgres の場合
    ```
    $ kubectl create -f kong_migration_postgres.yaml
    ```

    Cassandra の場合
    ```
    $ kubectl delete -f kong_migration_cassandra.yaml
    ```

4. Deploy Kong
    Postgres の場合
    ```
    $ kubectl create -f kong_postgres.yaml
    ```

    Cassandra の場合
    ```
    $ kubectl create -f kong_cassandra.yaml
    ```

6. Verify your deployments
    ```
    $ kubectl get all
    ```

7. クリーンアップ
    ```
    $ kubectl delete all
    ```

---

## Minikube
    [WIP]
