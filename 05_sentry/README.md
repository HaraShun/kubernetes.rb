# Sentry

## Minikube

1. VM上にk8sクラスターを作成

    ```
    $ minikube start --memory 4096 # デフォルトの2048でメモリが足りないため
    $ minikube dashboard # 起動するとわかりやすい
    ```

2. DB用のStatefulSet, Service, Secret作成

   ```
   $ kubectl apply -f k8s/db
   secret/db-secret created
   service/db-service created
   statefulset.apps/db-statefulset created
   ```

3. Redis用のStatefulSet, Service作成

   ```
   $ kubectl apply -f k8s/redis
   service/redis-service created
   statefulset.apps/redis-statefulset created
   ```

4. Sentry用のSecretのみ作成

   ```
   $ kubectl apply -f k8s/sentry/secret.yml
   secret/sentry-secret created
   ```

5. DBのマイグレーションとsuperuserの作成Jobを実行

   ```
   $ kubectl apply -f k8s/jobs/sentry-upgrade-job.yml
   job.batch/sentry-upgrade-job created

   # ジョブの実行を待つ
   $ kubectl get jobs -w
   NAME                 DESIRED   SUCCESSFUL   AGE
   sentry-upgrade-job   1         0            1m

   sentry-upgrade-job   1         1         3m

   $ kubectl apply -f k8s/jobs/sentry-createuser-job.yml
   job.batch/sentry-createuser-job created

   # ジョブの実行を待つ
   $ kubectl get jobs -w
   NAME                     DESIRED   SUCCESSFUL   AGE
   sentry-createuser-job   1         0            5s
   sentry-upgrade-job       1         1            3m

   sentry-createuser-job   1         1         6s
   ```

6. Sentry用のDeployment, StatefulSet, Service作成

   ```
   $ kubectl apply -f k8s/sentry
   deployment.apps/sentry-web-deployment created
   deployment.apps/sentry-cron-deployment created
   secret/sentry-secret unchanged
   service/sentry-service created
   statefulset.apps/sentry-worker-statefulset created
   ```

7. アプリケーションの検査と表示

    ```
    $ kubectl get services
    NAME             TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
    db-service       ClusterIP      10.108.254.104   <none>        5432/TCP       50m
    kubernetes       ClusterIP      10.96.0.1        <none>        443/TCP        10h
    redis-service    ClusterIP      10.98.99.146     <none>        6379/TCP       50m
    sentry-service   LoadBalancer   10.96.94.151     <pending>     80:30607/TCP   39m
    ```

8. sentry-serviceのURLを確認する

    ```
    $ minikube service sentry-service --url
    http://192.168.64.9:30607
    ```

9. クリーンアップ

    ```
    $ kubectl delete deployments --all
    $ kubectl delete statefulsets --all
    $ kubectl delete services --all
    $ kubectl delete secrets --all
    $ kubectl delete jobs --all
    $ kubectl delete persistentVolumeClaims --all
    $ minikube delete # 終了する場合
    ```

---

## GKE

WIP
