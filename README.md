# simple-mermaid-aws
AWSの構成図をMermaid記法でシンプルに書こう

## 参考サイト
[[Mermaid]MermaidでAWS構成図を書く](https://zenn.dev/xoxo/articles/f075f2b252280b)

## 前提
1. Visual Studio Code インストール済み
1. 以下の拡張機能をインストールする
1. Markdown Preview Mermaid Support
1. Markdown Preview Enhanced

## 手順

1. VSCode上で、コマンドパレット([Ctrl] + [Shift] + [P])から、「Markdown Preview Enhanced: Customize Preview HTML Head (Global)」を選択。
```
Markdown Preview Enhanced: Customize Preview HTML Head (Global)
```

2. 以下のコードをペースト
```
<!-- The content below will be included at the end of the <head> element. -->
<script type="text/javascript">
  const configureMermaidIconPacks = () => {
    window["mermaid"].registerIconPacks([
      {
        name: "logos",
        loader: () =>
          fetch("https://unpkg.com/@iconify-json/logos/icons.json").then(
            (res) => res.json()
          ),
      },
    ]);
  };
    
  // ref: https://stackoverflow.com/questions/39993676/code-inside-domcontentloaded-event-not-working
  if (document.readyState !== 'loading') {
    configureMermaidIconPacks();
  } else {
    document.addEventListener("DOMContentLoaded", () => {
      configureMermaidIconPacks();
    });
  }
</script>
``` 


## AWS構成図をコードで作図する

iConifyのサイトで使えるアイコンを探す。

https://icon-sets.iconify.design/?query=aws&search-page=1

### FutureRays労働組合のサイト(サーバーレス)
- Route53でドメインを取得(年額3ドル)し、ドメインを管理する(月額1ドル)
- ACMでSSL証明書を取得(無料)
- S3で静的コンテンツを保管する(月額数セント)
- CloudFront(CDN) 全世界へ低レイテンシーで配信中
- GitHub Actions(CI/CD)により記事がコミットされると、SSG(静的サイトジェネレータ)により記事が更新される

``` mermaid
architecture-beta
    service dns(logos:aws-route53)[Route53]
    service cdn(logos:aws-cloudfront)[CloudFront]
    service cert(logos:aws-certificate-manager)[ACM]

    service storage(logos:aws-s3)[S3]

    dns:R --> L:cdn
    cdn:T <-- B:storage

    cert:B --> T:dns
```

### SVNサーバ(アンマネージド)
- EC2(t4g.nano)インスタンスにAmazon Linux 2023を入れて利用中(夜間停止を行い月額6ドル程度)
- Elastic IPにより、グローバルIPアドレスを固定化(月額1ドルちょい)
- Cloud Trailにて、監査証跡を月次で保管中(無料)

``` mermaid
architecture-beta
  service EIP(logos:aws-eip)[ElasticIP]
  group vpc(logos:aws-vpc)[VPC]

  group private_subnet1[Private Subnet] in vpc
  service appserver1(logos:aws-ec2)[EC2 Instance] in private_subnet1
  
  EIP:R --> L:appserver1
```

## ローカルプレビューだといい感じなんだけどね
![alt text](local-preview.png)