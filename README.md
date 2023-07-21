# APISIX

apisix为api网关，本项目为二次开发的Consul支持和Identity Server4插件，其他文档参考[APISIX官网](http://apisix.apache.org/)获取

本文说一下不用consul的配置

### 启用Identity Server 4插件

1、在apisix节点把`identity-server4.lua`挂载到`/usr/local/apisix/apisix/plugins/`

```yml
        volumes: 
            - ./logs:/usr/local/apisix/logs
            - ./config/apisix_config.yaml:/usr/local/apisix/conf/config.yaml:ro
            - ./discovery/consul.lua:/usr/local/apisix/apisix/discovery/consul.lua:ro
            - ./plugins/identity-server4.lua:/usr/local/apisix/apisix/plugins/identity-server4.lua:ro
        ports:
            - "9180:9180/tcp"
            - "9080:9080/tcp"
            - "9091:9091/tcp"
            - "9443:9443/tcp"
            - "9092:9092/tcp"
```

2、启用docker compose
```
docker compose up -d
```
3、拿到最新的schema.json
```
curl http://127.0.0.1:9180/apisix/admin/plugins/reload -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT

curl 127.0.0.1:9092/v1/schema > schema.json # 用下载到的schema.json替换掉这个项目里面的schema.json
```
4、在apisix-dashboard节点把`schema.json`挂载到`/usr/local/apisix-dashboard/conf/`

```yml
        volumes: 
            - ./config/apisix_dashboard_config.yaml:/usr/local/apisix-dashboard/conf/conf.yaml:ro
            - ./config/schema.json:/usr/local/apisix-dashboard/conf/schema.json:ro
```

5 修改 `apisix_config.yaml`和`apisix_dashboard_config.yaml`添加`plugins`配置
```yml
plugins:                          # plugin list (sorted in alphabetical order)
  - identity-server4
```
6、确保apisix、identityserver4、上游服务都在同一个网络

7、启动容器

```bash
docker-compose up -d
```

8、在dashboard启用插件并添加配置



采用公钥自省

```json
{
  "introspect_type": "key",
  "issuer": "https://your.identity.server",
  "public_key": "-----BEGIN PUBLIC KEY-----Your Public Key-----END PUBLIC KEY-----"
}
```
采用IdentityServer4自省
```json
{
  "introspect_type": "issuer",
  "issuer": "https://your.identity.server",
  "api_name": "your api name",
  "api_secrets": "your api secrets"
}
```
采用curl自省
```

curl http://127.0.0.1:9180/apisix/admin/routes/1 \
-H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT -i -d '
{
    "uri": "/index.html",
    "hosts": ["foo.com", "*.bar.com"],
    "remote_addrs": ["127.0.0.0/8"],
    "methods": ["PUT", "GET"],
    "enable_websocket": true,
    "upstream": {
        "type": "roundrobin",
        "nodes": {
            "127.0.0.1:1980": 1
        }
    },
    "plugins": {
        "proxy-rewrite": "roundrobin",
        "identity-server4": {
             "introspect_type": "issuer",
             "issuer": "https://your.identity.server",
             "api_name": "your api name",
             "api_secrets": "your api secrets"
         }
    }
            - ./config/apisix_dashboard_config.yaml:/usr/local/apisix-dashboard/conf/conf.yaml:ro
            - ./config/schema.json:/usr/local/apisix-dashboard/conf/schema.json:ro
```


5 修改 `apisix_config.yaml`和`apisix_dashboard_config.yaml`添加`plugins`配置
```yml
plugins:                          # plugin list (sorted in alphabetical order)
  - identity-server4
```
6、确保apisix、identityserver4、上游服务都在同一个网络


7、启动容器


```bash
docker-compose up -d
```


8、在dashboard启用插件并添加配置






采用公钥自省


```json
{
  "introspect_type": "key",
  "issuer": "https://your.identity.server",
  "public_key": "-----BEGIN PUBLIC KEY-----Your Public Key-----END PUBLIC KEY-----"
}
```
采用IdentityServer4自省
```json
{
  "introspect_type": "issuer",
  "issuer": "https://your.identity.server",
  "api_name": "your api name",
  "api_secrets": "your api secrets"
}
```
采用curl自省
```


curl http://127.0.0.1:9180/apisix/admin/routes/1 \
-H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT -i -d '
{
    "uri": "/index.html",
    "hosts": ["foo.com", "*.bar.com"],
    "remote_addrs": ["127.0.0.0/8"],
    "methods": ["PUT", "GET"],
    "enable_websocket": true,
    "upstream": {
        "type": "roundrobin",
        "nodes": {
            "127.0.0.1:1980": 1
        }
    },
    "plugins": {
        "proxy-rewrite": "roundrobin",
        "identity-server4": {
             "introspect_type": "issuer",
             "issuer": "https:your.identity.server",
             "api_name": "your api name",
             "api_secrets":"your api secrets"
   }
    }
}'
```




| data type| Required| Description| 说明 |
|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|
| string |Required| 必填                                                                                      | 自省方式，枚举类型，可选"issuer"、"key"                                                     |
| string |Required| URL of IdentityServer4| IdentityServer4的url |
| string |Required if introspect_type = "issuer"| The api resource name registered by apisix with identityserver4| apisix在identityserver4注册的api resource name |
| string |Required if introspect_type = "issuer"| apisix registered api resource secrets in identityserver4| apisix在identityserver4注册的api resource secrets |
| string |Required if prospect_type = "key"| Identity Server 4 uses the certificate's rsa public key| IdentityServer4使用证书的rsa公钥 |            - ./config/apisix_dashboard_config.yaml:/usr/local/apisix-dashboard/conf/conf.yaml:ro
            - ./config/schema.json:/usr/local/apisix-dashboard/conf/schema.json:ro
```


5 修改 `apisix_config.yaml`和`apisix_dashboard_config.yaml`添加`plugins`配置
```yml
plugins:                          # plugin list (sorted in alphabetical order)
  - identity-server4
```
6、确保apisix、identityserver4、上游服务都在同一个网络


7、启动容器


```bash
docker-compose up -d
```


8、在dashboard启用插件并添加配置






采用公钥自省


```json
{
  "introspect_type": "key",
  "issuer": "https://your.identity.server",
  "public_key": "-----BEGIN PUBLIC KEY-----Your Public Key-----END PUBLIC KEY-----"
}
```
采用IdentityServer4自省
```json
{
  "introspect_type": "issuer",
  "issuer": "https://your.identity.server",
  "api_name": "your api name",
  "api_secrets": "your api secrets"
}
```
采用curl自省
```


curl http://127.0.0.1:9180/apisix/admin/routes/1 \
-H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT -i -d '
{
    
    "uri": "/routername/*",
    "name": "routername",
    "remote_addrs": ["127.0.0.0/8"],
    "methods": ["PUT", "GET"],
    "enable_websocket": true,
    "upstream": {
        "type": "roundrobin",
        "nodes": {
            "127.0.0.1:1980": 1
        }
    },
    "plugins": {
        "proxy-rewrite": "proxy-rewrite": {
              "regex_uri": [
                 "^//routername(.*)",
                 "/$1"
              ],
             "use_real_request_uri_unsafe": false
         },
        "identity-server4": {
             "introspect_type": "issuer",
             "issuer": "https:your.identity.server",
             "api_name": "your api name",
             "api_secrets":"your api secrets"
   }
    }
}'
```




| data type| Required| Description| 说明 |
|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|
| string |Required| 必填                                                                                      | 自省方式，枚举类型，可选"issuer"、"key"                                                     |
| string |Required| URL of IdentityServer4| IdentityServer4的url |
| string |Required if introspect_type = "issuer"| The api resource name registered by apisix with identityserver4| apisix在identityserver4注册的api resource name |
| string |Required if introspect_type = "issuer"| apisix registered api resource secrets in identityserver4| apisix在identityserver4注册的api resource secrets |
| string |Required if prospect_type = "key"| Identity Server 4 uses the certificate's rsa public key| IdentityServer4使用证书的rsa公钥 |
```


| 字段 | 数据类型 | 必填 | 说明 |
|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|
|introspect_type | string | 必填                                                                                      | 自省方式，枚举类型，可选"issuer"、"key"                                                     |
|issuer | string | 必填 | IdentityServer4的url |
|api_name | string | 如果introspect_type = "issuer" 则为必填 | apisix在identityserver4注册的api resource name |
|api_secrets | string | 如果introspect_type  = "issuer" 则为必填 | apisix在identityserver4注册的api resource secrets |
|public_key | string | 如果introspect_type  = "key" 则为必填 | IdentityServer4使用证书的rsa公钥 |
