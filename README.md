# nuxt-storybook

## install Nuxt

まずは通常通りNuxtプロジェクトをcreateする。
https://nuxtjs.org/docs/get-started/installation

```
$ yarn create nuxt-app <project-name>
```
おそらく起動するだろう

```
$ cd <project-name>
$ yarn dev
```

## install nuxt-storybook

基本的に公式通りに行うとよいが、この後起動するとエラーが起こるので調べたらここのコミュニティで解決策があった。
core-jsのaddとnuxt.config.jsへの記述を忘れすに。

https://github.com/nuxt/nuxt.js/issues/5287#issuecomment-622660147

```
$ yarn nuxt storybook
```

解決し、起動できた。


## Tailwind CSSのインストール

公式通りに行う

https://tailwindcss.com/docs/guides/nuxtjs


## SCSSを使えるように

たいてい、nuxtをインストールするだけだとSCSSを使うときここで引っかかる。

```
$ yarn add --dev node-sass sass-loader @nuxtjs/style-resources
```
エラーが起こる、原因の詳細は以下に示すが、これで動作した。

```
$ yarn remove node-sass sass-loader
```

私の環境に合わせ、node-sassのバージョンを調べる、調べ方は下記に示しておいた。

（node 14.16.0の場合）
```
$ yarn add -D sass-loader@10.2.0 node-sass@4.14.1
```


### sass-loaderのバージョンを落とす

nuxtにインストールされるWebpackのバージョンが4.xだが、sass-loaderはWebpackの5以降じゃないと対応しない。
なので、sass-loaderのバージョンを落として11じゃなく、10.xを使うことにする。

### node-sassのバージョンを使っているnode.jsのバージョンに対応させる

node-sassはnode.jsバージョンに対応させて使う必要があるため
https://github.com/sass/node-sass
ここを読み、 `node -v`して自分の環境と合わせる。

### @nuxtjs/style-resources も入れる

これを入れると、mixinsなどがコンポーネント外部から参照できるようになり便利であるので一緒に入れる。

## 起動

Nuxt
```
$ yann dev
```

Storybook
```
$ yarn nuxt storybook
```

## Google Cloud Platform, Google App Engineにデプロイ

GCP側でプロジェクトを作成する

app.yamlファイルを公式通り、ルートに置く
https://nuxtjs.org/ja/deployments/google-appengine/

ただし、SPAモードの場合はうまくいかない、サーバーエラーが起こる。
理由は静的ファイルとして出力したディレクトリやパスが違う。
SPAモードの場合、以下のようにhandlers以下に変更を加える必要がある

```
runtime: nodejs14
instance_class: F2
handlers:
  - url: /dist
    static_dir: dist
    secure: always
  - url: /(.*\.(gif|png|jpg|ico|txt|svg))$
    static_files: static/\1
    upload: static/.*\.(gif|png|jpg|ico|txt|svg)$
    secure: always
  - url: /.*
    script: auto
    secure: always
env_variables:
  HOST: '0.0.0.0'

```

デプロイ前にgenerateしとく
```
$ yarn generate
```

```
$ gcloud app deploy app.yaml --project <PROJECT-ID>
```

ブラウザで確認する。
