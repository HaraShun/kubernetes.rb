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

3. kubectl をインストール

    ```
    $ gcloud components install kubectl
    ```

4. プロジェクトを作成

    ```
    $ gcloud projects create <PROJECT-ID>
    $ gcloud projects delete <PROJECT-ID>
    $ gcloud projects list
    ```

5. プロジェクトを設定

    ```
    $ gcloud config list
    $ gcloud config set project <PROJECT-ID>
    $ gcloud config get-value project
    ```

6. 支払い情報を入力（GUI）

    https://console.developers.google.com/project/{PROJECT-ID}/settings

7. Kubernetes Engine APIを有効化

    ```
    $ gcloud services enable container.googleapis.com
    $ gcloud services list --available
    ```

---

## 参考

- [kubectl Cheat Sheet - Kubernetes](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
