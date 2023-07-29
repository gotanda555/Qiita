---
title: ransackで、パラメータに空白が含まれていても許可するよう設定する
tags:
  - Ruby
  - Rails
  - Gem
  - ransack
private: false
updated_at: '2023-07-29T22:04:22+09:00'
id: 451d41029171275ebbe4
organization_url_name: null
slide: false
---

# はじめに

Rails の gem で、ransack を使用時に、現状の仕様（v4.0.0）だと、パラメータの中に空白があったら自動的に削除されてしまいます。
それを空白があった場合でも許可するように設定する必要がありました。対処法を以下にまとめましたので、良ければご覧ください。

# 結論

`config/initializers/ransack.rb`に、`c.strip_whitespace = false`を追加すれば、空白も許可してくれるようになります。

```ruby: config/initializers/ransack.rb
Ransack.configure do |c|
  # Change whitespace stripping behaviour.
  # Default is true
  c.strip_whitespace = false ## 追加
end
```

:::note warn
ディレクトリ構成は違う可能性がありますので、ご自身の構成に合わせて修正をお願いいたします。
:::

# なぜ c.strip_whitespace = false を設定する必要があるのか

## ransack の以前の仕様

ransack のバージョンが`2.3.2`までは、パラメータの中に空白が含まれていても、空白を含む文字列として検索されていました。

```shell
[2] pry(#<Api::AbcController>)> Abc.ransack(encrypted_sanitized_tel_or_encrypted_tel_eq: Abc.encrypt(:tel, params[:abcde]))
=> Ransack::Search<class: Abc, base: Grouping <conditions: [Condition <attributes: ["encrypted_sanitized_tel", "encrypted_tel"], predicate: eq, combinator: or, values: ["abcdefg12345678==\n"]>], combinator: and>>
```

```shell
values: ["abcdefg12345678==\n"]
```

:::note warn
Controller 名やカラム名などは、適当に記載しております。ご自身の実装に合わせて考えていただけたらと思います。
:::

ただ、`2.4.0`から`2.4.2`の間は、パラメータの中に空白が含まれていたら、自動的に削除されるよう修正が入っていました。

- [ransack 2.4.0 CHANGELOG.md](https://github.com/activerecord-hackery/ransack/blob/main/CHANGELOG.md#240---2020-11-27)
- [空白が自動削除されるようになった修正 PR](https://github.com/activerecord-hackery/ransack/pull/1126)

```shell
[2] pry(#<Api::AbcController>)> Abc.ransack(encrypted_sanitized_tel_or_encrypted_tel_eq: Abc.encrypt(:tel, params[:abcde]))
=> Ransack::Search<class: Abc, base: Grouping <conditions: [Condition <attributes: ["encrypted_sanitized_tel", "encrypted_tel"], predicate: eq, combinator: or, values: ["abcdefg12345678=="]>], combinator: and>>
```

```shell
values: ["abcdefg12345678=="]
```

なので、もともと ransack のバージョンが 2.3.2 以前で、2.4.0 にバージョンアップすると、仮に実装で、空白も含めた文字列の検索処理をしているところがあると、不具合が起きると思います。（自分はこれでテストでエラーが多発しました、、、）

## 2.5.0 以降の仕様

自動で空白が削除されるようになったのはいいですが、それを回避するためのオプションが用意されていなかったため、ransack を使用している実装を大幅に変更する必要があったと思います。

そこで、`2.5.0`で空白を含んでもいいようなオプションが追加されました。それが`c.strip_whitespace = false`です。

- [ransack 2.5.0 CHANGELOG.md](https://github.com/activerecord-hackery/ransack/blob/main/CHANGELOG.md#250---2021-12-26)
- [空白も許可できるようになった修正 PR](https://github.com/activerecord-hackery/ransack/pull/1214)

この実装があったおかげで、ransack のバージョンをあげても、実装で空白も含む文字列で検索している場合は、オプションを追加すれば問題なくなりました。

これで無事解決ですね 👍

:::note alert
上記の内容は、現在（2023/07）での README には記載がありません。
GitHub で確認したい場合は、`main > Tags > v2.5.0` にすると、README で確認することができます。

https://github.com/activerecord-hackery/ransack
https://github.com/activerecord-hackery/ransack/tree/v2.5.0
:::

# まとめ

上記にも記載しましたが、現状の README に記載がなかったため、CHANGELOG を一から追ってようやく見つけることができました。CHAGELOG は、リリースされた内容が全て記載されているので、該当のバージョンの仕様を知るには、最適な資料でした。

CHANGELOG を読むのは大変だとは思いますが、何かのバージョンをあげる際には、必ず使う資料ですので、一緒に頑張っていきましょう！

最後までご視聴いただき、ありがとうございました。
この記事が、少しでも皆さんのお役に立てれたら幸いです。
