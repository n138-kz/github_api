# GitHub API

## Token

[Issue token](https://github.com/settings/tokens)

### Permmision

| 必須 | 権限名 | 備考 |
|:-:|:-|:-|
| 必要に応じて | admin:repo_hook | Full control of repository hooks |
| 必要に応じて | write:repo_hook | Write repository hooks |
| 必須 | read:repo_hook  | Read repository hooks |

```sh
token=''
```

## Webhook

[REST API/リポジトリ/ウェブフックs](https://docs.github.com/ja/rest/repos/webhooks?apiVersion=2022-11-28)

### List

| 必須 | 権限名 | 備考 |
|:-:|:-|:-|
| No | admin:repo_hook | Full control of repository hooks |
| No | write:repo_hook | Write repository hooks |
| Yes | read:repo_hook  | Read repository hooks |

```sh
for i in $(curl 'https://api.github.com/users/n138-kz/repos?sort=name&per_page=1000' | jq -r .[].url); do
    curl -L \
        -H "Accept: application/vnd.github+json" \
        -H "Authorization: Bearer ${token}" \
        -H "X-GitHub-Api-Version: 2022-11-28" \
        ${i}/hooks
done
```

### Create(add)

| 必須 | 権限名 | 備考 |
|:-:|:-|:-|
| No | admin:repo_hook | Full control of repository hooks |
| Yes | write:repo_hook | Write repository hooks |
| Yes | read:repo_hook  | Read repository hooks |

```sh
for i in $(curl 'https://api.github.com/users/n138-kz/repos?sort=name&per_page=1000' | jq -r .[].url); do
    curl -L \
        -X POST \
        -H "Accept: application/vnd.github+json" \
        -H "Authorization: Bearer ${token}" \
        -H "X-GitHub-Api-Version: 2022-11-28" \
        ${i}/hooks \
        -d '{"name":"web","active":true,"events":["*"],"config":{"url":"https://discord.com/api/webhooks/HOGE/FOO/github","content_type":"json","insecure_ssl":"0"}}'
done
```

### Delete

| 必須 | 権限名 | 備考 |
|:-:|:-|:-|
| No | admin:repo_hook | Full control of repository hooks |
| Yes | write:repo_hook | Write repository hooks |
| Yes | read:repo_hook  | Read repository hooks |

```sh
for i in $(\
    curl 'https://api.github.com/users/n138-kz/repos?sort=name&per_page=1000'\
    | jq -r .[].url\
); do
    for j in $(\
        curl -L \
        -H "Accept: application/vnd.github+json" \
        -H "Authorization: Bearer ${token}" \
        -H "X-GitHub-Api-Version: 2022-11-28" \
        ${i}/hooks | jq -r .[].url \
    ); do
        curl -L \
            -X DELETE \
            -H "Accept: application/vnd.github+json" \
            -H "Authorization: Bearer ${token}" \
            -H "X-GitHub-Api-Version: 2022-11-28" \
            ${j} \
        &
    done \
&
done
```
