# Mastodon

* httpsでの接続が必須な関係で手順はminikubeのみとなります
* 簡単のため、シングルユーザーモードでの起動となります
* SMTPサーバーの用意ができなかったため、メール確認は無理やり通します
* 今回WebSocket対応は出来てません
* 最新バージョン (v2.4.3) のMastodonではいくつかバグがあったため、Dockerイメージは `tootsuite/mastodon:edge` を使っています

## Minikube

1. VM上にk8sクラスターを作成

    ```
    $ minikube start --memory 8192 # デフォルトの2048でメモリが足りないため
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

4. Mastodon用のSecretのみ作成

    ```
    $ kubectl apply -f k8s/mastodon/secret.yaml
    secret/mastodon-secret created
    ```

5. DBのマイグレーションを実行

    ```
    $ kubectl apply -f k8s/jobs/db-create-job.yaml
    job.batch/db-create-job created

    # ジョブの実行を待つ
    $ kubectl get jobs -w
    NAME            DESIRED   SUCCESSFUL   AGE
    db-create-job   1         1            3m

    $ kubectl apply -f k8s/jobs/db-migrate-job.yaml
    job.batch/db-migrate-jobb created

    # ジョブの実行を待つ
    $ kubectl get jobs -w
    NAME              DESIRED   SUCCESSFUL   AGE
    db-create-job     1         1            5m
    db-migrate-jobb   1         1            1m
    ```

6. MinikubeのIPアドレスでmastodon.localにアクセスできるように`/etc/hosts`に書き込む (httpsでアクセスするため)

    ```
    $ minikube ip
    192.168.64.17

    $ sudo vi /etc/hosts
    $ sudo tail -n1 /etc/hosts
    192.168.64.17   mastodon.local
    ```

7. ServiceのexternalIPsにMinikubeのIPアドレスを指定する (httpsでアクセスするため)

    ```
    $ vi k8s/mastodon/service.yaml
    $ tail -n2 k8s/mastodon/service.yaml
    externalIPs:
    - 192.168.64.17
    ```

8. Mastodon用のDeployment, Service作成

    ```
    $ kubectl apply -f k8s/mastodon
    deployment.apps/mastodon-web-deployment created
    deployment.apps/mastodon-worker-deployment created
    secret/mastodon-secret unchanged
    service/mastodon-service created
    ```

7. アプリケーションの検査と表示

    ```
    $ kubectl get services
    NAME               TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                      AGE
    db-service         ClusterIP      10.96.20.178    <none>          5432/TCP                     11m
    kubernetes         ClusterIP      10.96.0.1       <none>          443/TCP                      12m
    mastodon-service   LoadBalancer   10.100.43.202   192.168.64.17   80:31878/TCP,443:32696/TCP   5m
    redis-service      ClusterIP      10.98.107.176   <none>          6379/TCP                     11m
    ```

8. `https://mastodon.local` を確認
    * アクセス出来るようになるまで時間がかかります
    * 自己署名証明書を使っているため、ブラウザで警告画面が出ますがそのまま開いてください

    ![image](./mastodon_top.png)

9. 画面左上から自分のログイン情報を入力してサインアップ
    * メールアドレスは実在するものでないといけません
    * パスワードは任意のものを入力

    ![image](./mastodon_signup.png)

10. JobのYaml内にそれぞれ先ほど入力したユーザー情報を指定
    * SMTPサーバーの用意ができなかったため、メール確認はRakeタスクで通します

    ```
    $ vi k8s/jobs/confirm-email-job.yaml
    $ tail -n2 jobs/confirm-email-job.yaml
        - name: USER_EMAIL
          value: tsubasatakayama511@gmail.com

    $ vi k8s/jobs/make-admin-job.yaml
    $ tail -n2 jobs/make-admin-job.yaml
        - name: USERNAME
          value: tsub
    ```

11. メール確認と管理者権限付与のJobを実行

    ```
    $ kubectl apply -f k8s/jobs/confirm-email-job.yaml
    job.batch/confirm-email-job created

    # ジョブの実行を待つ
    $ kubectl get jobs -w
    NAME                DESIRED   SUCCESSFUL   AGE
    confirm-email-job   1         1            22s
    db-create-job       1         1            30m
    db-migrate-jobb     1         1            26m

    $ kubectl apply -f k8s/jobs/make-admin-job.yaml
    job.batch/make-admin-job created

    # ジョブの実行を待つ
    $ kubectl get jobs -w
    NAME                DESIRED   SUCCESSFUL   AGE
    confirm-email-job   1         1            56s
    db-create-job       1         1            30m
    db-migrate-jobb     1         1            26m
    make-admin-job      1         1            15s
    ```

12. `https://mastodon.local/auth/sign_in` を確認
13. 先ほど入力したユーザー情報を入力してサインイン

    ![image](./mastodon_singin.png)

14. サインインできることを確認

    ![image](./mastodon_hello.png)

15. クリーンアップ

    ```
    $ kubectl delete deployments --all
    $ kubectl delete statefulsets --all
    $ kubectl delete services --all
    $ kubectl delete secrets --all
    $ kubectl delete jobs --all
    $ kubectl delete persistentVolumeClaims --all
    $ minikube delete # 終了する場合
    ```
