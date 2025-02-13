---
title: 移行
intro: 'The Migration API lets you migrate the repositories and users of your  organization from {% data variables.product.prodname_dotcom_the_website %} to {% data variables.product.prodname_ghe_server %}.'
redirect_from:
  - /v3/migrations
  - /v3/migration
  - /v3/migration/migrations
versions:
  fpt: '*'
  ghec: '*'
  ghes: '>3.3'
  ghae: issue-6184
topics:
  - API
miniTocMaxHeadingLevel: 3
---

{% for operation in currentRestOperations %}
  {% unless operation.subcategory %}{% include rest_operation %}{% endunless %}
{% endfor %}

## Organization

移行 API を使用できるのは、認証済みの Organization オーナーのみです。 For more information, see "[Roles in an organization](/organizations/managing-peoples-access-to-your-organization-with-roles/roles-in-an-organization#permission-levels-for-an-organization)" and "[Other authentication methods](/rest/overview/other-authentication-methods)."

{% data variables.migrations.organization_migrations_intro %}

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'orgs' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% ifversion fpt or ghec %}
## ソースのインポート

{% data variables.migrations.source_imports_intro %}

一般的なソースのインポートでは、まずインポートを開始し、次に (オプションで) 作者を更新したり、インポートに大きいファイルが存在する場合に Git LFS 使用の設定を更新したりします。 [`RepositoryImportEvent`](/developers/webhooks-and-events/webhook-events-and-payloads#repository_import) をリスニングする webhook を作成して、インポートのステータスを確認することもできます。

詳細な例は、次の図を参照してください。

```
+---------+                     +--------+                              +---------------------+
| Tooling |                     | GitHub |                              | Original Repository |
+---------+                     +--------+                              +---------------------+
     |                              |                                              |
     |  Start import                |                                              |
     |----------------------------->|                                              |
     |                              |                                              |
     |                              |  Download source data                        |
     |                              |--------------------------------------------->|
     |                              |                        Begin streaming data  |
     |                              |<---------------------------------------------|
     |                              |                                              |
     |  Get import progress         |                                              |
     |----------------------------->|                                              |
     |       "status": "importing"  |                                              |
     |<-----------------------------|                                              |
     |                              |                                              |
     |  Get commit authors          |                                              |
     |----------------------------->|                                              |
     |                              |                                              |
     |  Map a commit author         |                                              |
     |----------------------------->|                                              |
     |                              |                                              |
     |                              |                                              |
     |                              |                       Finish streaming data  |
     |                              |<---------------------------------------------|
     |                              |                                              |
     |                              |  Rewrite commits with mapped authors         |
     |                              |------+                                       |
     |                              |      |                                       |
     |                              |<-----+                                       |
     |                              |                                              |
     |                              |  Update repository on GitHub                 |
     |                              |------+                                       |
     |                              |      |                                       |
     |                              |<-----+                                       |
     |                              |                                              |
     |  Map a commit author         |                                              |
     |----------------------------->|                                              |
     |                              |  Rewrite commits with mapped authors         |
     |                              |------+                                       |
     |                              |      |                                       |
     |                              |<-----+                                       |
     |                              |                                              |
     |                              |  Update repository on GitHub                 |
     |                              |------+                                       |
     |                              |      |                                       |
     |                              |<-----+                                       |
     |                              |                                              |
     |  Get large files             |                                              |
     |----------------------------->|                                              |
     |                              |                                              |
     |  opt_in to Git LFS           |                                              |
     |----------------------------->|                                              |
     |                              |  Rewrite commits for large files             |
     |                              |------+                                       |
     |                              |      |                                       |
     |                              |<-----+                                       |
     |                              |                                              |
     |                              |  Update repository on GitHub                 |
     |                              |------+                                       |
     |                              |      |                                       |
     |                              |<-----+                                       |
     |                              |                                              |
     |  Get import progress         |                                              |
     |----------------------------->|                                              |
     |        "status": "complete"  |                                              |
     |<-----------------------------|                                              |
     |                              |                                              |
     |                              |                                              |
```

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'source-imports' %}{% include rest_operation %}{% endif %}
{% endfor %}
{% endif %}
## ユーザ

ユーザ移行 API を使用できるのは、認証済みのアカウントオーナーのみです。 詳細は、「[その他の認証方式](/rest/overview/other-authentication-methods)」を参照してください。

{% data variables.migrations.user_migrations_intro %}ダウンロードできる移行データのリストは、「[ユーザー移行アーカイブをダウンロードする](#download-a-user-migration-archive)」を参照してください。

アーカイブをダウンロードするには、先にユーザ移行を開始する必要があります。 移行のステータスが `exported` になると、移行をダウンロードできます。

移行アーカイブを作成すると、7 日間ダウンロードできるようになります。 ただし、必要があればユーザ移行アーカイブはそれより以前に削除できます。 移行が `exported` になってからリポジトリのロックを解除すると、リポジトリの使用を再開でき、ソースデータが不要な場合にはリポジトリを削除することもできます。

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'users' %}{% include rest_operation %}{% endif %}
{% endfor %}
