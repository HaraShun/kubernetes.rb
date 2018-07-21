# Chapter 0: 準備

1. minikube をインストール

    ```
    $ curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.28.0/minikube-darwin-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
    ```

2. gcloud をインストールして初期化

    ```
    $ curl https://sdk.cloud.google.com | bash
    $ exec -l $SHELL
    $ which gcloud
    $ gcloud init
    ```

    参考: https://qiita.com/kentarosasaki/items/2232113b44b016a56adc

3. 使用するリージョン、ゾーンを設定

    ```
    $ gcloud config set compute/region [COMPUTE_REGION]
    $ gcloud config set compute/zone [COMPUTE_ZONE]

    # 例
    $ gcloud config set compute/region asia-northeast1
    $ gcloud config set compute/zone asia-northeast1-b
    ```

4. kubectl をインストール

    ```
    $ gcloud components install kubectl
    ```

5. プロジェクトを作成

    ```
    $ gcloud projects create <PROJECT-ID>
    $ gcloud projects delete <PROJECT-ID>
    $ gcloud projects list
    ```

6. プロジェクトを設定

    ```
    $ gcloud config list
    $ gcloud config set project <PROJECT-ID>
    $ gcloud config get-value project
    ```

7. 支払い情報を入力（GUI）

    https://console.developers.google.com/project/{PROJECT-ID}/settings

8. Kubernetes Engine APIを有効化

    ```
    $ gcloud services enable container.googleapis.com
    $ gcloud services list --available
    ```

---

## 参考

- [kubectl Cheat Sheet - Kubernetes](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
