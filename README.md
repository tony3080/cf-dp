# cloudflare-docker-proxy

![deploy](https://github.com/ciiiii/cloudflare-docker-proxy/actions/workflows/deploy.yaml/badge.svg)

> If you're looking for proxy for helm, maybe you can try [cloudflare-helm-proxy](https://github.com/ciiiii/cloudflare-helm-proxy).

## 部署
[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/Aqr-K/cf-dp)

1. fork这个项目
2. 将上述按钮的链接修改到您的fork网址
3. 单击该按钮，您将被重定向到“部署”页面（直接点击按钮也行）

## 配置教程

1. 使用 Cloudflare Worker Host：仅支持代理一个注册表
   - 方法：
     - 1. 替换`${workername}.${username}.workers.dev/`为Cloudflare生成的路由，如`cloudflare-docker-proxy.xxxx.workers.dev/`
   ```javascript
   const routes = {
     "${workername}.${username}.workers.dev/": "https://registry-1.docker.io",
   };
   ```
   
2. 使用自定义域或者路由：支持按主机代理多个注册管理机构路由（部分机构含有多种）
   - 方法1：
     - 1. 在 Cloudflare 上托管您的域`DNS`
     - 2. 添加`xxx.example.com`的`A`记录到`192.0.2.1`(IP没要求，随便填写就行，最后都会被worker截断，不会访问到填入的ip下)
     - 3. 将此项目部署到 Cloudflare Workers
     - 4. 添加 `xxx.example.com/*` 到 `路由`里
     - 5. 添加更多记录并根据需要修改配置，（左边为你的域名，右边为目标网站，左边每一个新域名，都需要单独做一次步骤b与步骤d）


   - 方法2：
     - 1. 将此项目部署到 Cloudflare Workers
     - 2. 添加`xxx.example.com`到`自定义域`
     - 3. 添加更多记录并根据需要修改配置，（左边为访问域名，右边为目标网站，左边每一个新域名，都需要单独做一次步骤b）

   - 需要修改的源代码
   ```javascript
   const routes = {
        "docker.example.com": "https://registry-1.docker.io",
         // 允许添加单个站点  
      };
   ```

## 目前发现存在的问题

1. 部分`docker`官方发布的容器，如`hello-world`会`pull`失败，出现提示：
   - `Error response from daemon: pull access denied for xxx.example.com/hello-world, repository does not exist or may require 'docker login': denied: requested access to the resource is denied`
