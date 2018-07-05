# Rails + Postgres + Nginx

https://github.com/yhirano55/react-redux-jwt-authentication-example/

## Minikube

1. VM上にk8sクラスターを作成

    ```
    $ minikube start
    $ minikube dashboard # 起動するとわかりやすい
    ```

2. DB用のStatefulSet, Service, Secret作成

   ```
   $ kubectl apply -f k8s/db
   secret/db-secret created
   service/db-service created
   statefulset.apps/db-statefulset created
   ```

3. Rails用のDeployment, Service, Secret, ConfigMap作成

   ```
   $ kubectl apply -f k8s/rails
   configmap/rails-nginx-config created
   deployment.apps/rails-deployment created
   secret/rails-secret created
   service/rails-service created
   ```

4. DBの作成とマイグレーション、初期データの投入Jobを実行

   ```
   $ kubectl apply -f k8s/jobs/rails-db-create-job.yml
   job.batch/rails-db-create-job created

   $ kubectl apply -f k8s/jobs/rails-db-migrate-job.yml
   job.batch/rails-db-migrate-job created

   $ kubectl apply -f k8s/jobs/rails-db-seed-job.yml
   job.batch/rails-db-seed-job created
   ```

5. アプリケーションの検査と表示

    ```
    $ kubectl get services
    NAME            TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
    db-service      ClusterIP      10.106.89.36     <none>        5432/TCP       1m
    kubernetes      ClusterIP      10.96.0.1        <none>        443/TCP        1h
    rails-service   LoadBalancer   10.102.124.116   <pending>     80:31037/TCP   1m
    ```

6. rails-serviceのURLを確認する

    ```
    $ minikube service rails-service --url
    http://192.168.64.7:31037
    ```

7. クリーンアップ

    ```
    $ kubectl delete deployments --all
    $ kubectl delete statefulsets --all
    $ kubectl delete services --all
    $ kubectl delete secrets --all
    $ kubectl delete configmap --all
    $ kubectl delete jobs --all
    $ kubectl delete persistentVolumeClaims --all
    $ minikube delete # 終了する場合
    ```

---

## GKE

1. GKE上にk8sクラスターを作成

    ```
    $ gcloud container clusters create {CLUSTER_NAME}
    ```

2. DB用のStatefulSet, Service, Secret作成

   ```
   $ kubectl apply -f k8s/db
   secret/db-secret created
   service/db-service created
   statefulset.apps/db-statefulset created
   ```

3. Rails用のDeployment, Service, Secret, ConfigMap作成

   ```
   $ kubectl apply -f k8s/rails
   configmap/rails-nginx-config created
   deployment.apps/rails-deployment created
   secret/rails-secret created
   service/rails-service created
   ```

4. DBの作成とマイグレーション、初期データの投入Jobを実行

   ```
   $ kubectl apply -f k8s/jobs/rails-db-create-job.yml
   job.batch/rails-db-create-job created

   $ kubectl apply -f k8s/jobs/rails-db-migrate-job.yml
   job.batch/rails-db-migrate-job created

   $ kubectl apply -f k8s/jobs/rails-db-seed-job.yml
   job.batch/rails-db-seed-job created
   ```

5. アプリケーションの検査と表示

    ```
    $ kubectl get services
    NAME            TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
    db-service      ClusterIP      10.55.254.24    <none>          5432/TCP       3m
    kubernetes      ClusterIP      10.55.240.1     <none>          443/TCP        29m
    rails-service   LoadBalancer   10.55.245.147   35.200.89.253   80:30232/TCP   2m
    ```

6. `http://{EXTERNAL-IP}` を確認

7. クリーンアップ

    ```
    $ kubectl delete services --all
    $ kubectl delete persistentVolumeClaims --all
    $ gcloud container clusters delete {CLUSTER_NAME}
    ```
