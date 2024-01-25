# cloudflare-docker-proxy

![deploy](https://github.com/ciiiii/cloudflare-docker-proxy/actions/workflows/deploy.yaml/badge.svg)

> If you're looking for proxy for helm, maybe you can try [cloudflare-helm-proxy](github.com/ciiiii/cloudflare-helm-proxy).

## Deploy
[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/jiamliang/cloudflare-docker-proxy)

1. fork this project
2. modify the link of the above button to your fork url
3. click the button, you will be redirected to the deploy page

在跳转的页面中，会让你填写 account id 和 API token，建议先去创建一个 API token
注意： account id 不是登录名，在 workers 页面能看到一个比较长的字符串。如果没有的话，先随便创建一个 applications，就能看到了。

## Config tutorial

1. use cloudflare worker host: only support proxy one registry
   ```javascript
   const routes = {
     "${workername}.${username}.workers.dev/": "https://registry-1.docker.io",
   };
   ```
2. use custom domain: support proxy multiple registries route by host
   - host your domain DNS on cloudflare
   - add `A` record of xxx.example.com to `192.0.2.1`
   - deploy this project to cloudflare workers
   - add `xxx.example.com/*` to HTTP routes of workers
   - add more records and modify the config as you need
   ```javascript
   const routes = {
     "docker.libcuda.so": "https://registry-1.docker.io",
     "quay.libcuda.so": "https://quay.io",
     "gcr.libcuda.so": "https://k8s.gcr.io",
     "k8s-gcr.libcuda.so": "https://k8s.gcr.io",
     "ghcr.libcuda.so": "https://ghcr.io",
   };
   ```
关于添加 custom domain：
- host your domain DNS on cloudflare ：这个就是把你的域名解析转到 cloudflare 来，让你可以在 cloudflare 里面设置你的 DNS 记录。**可以提前做，也可以部署完成之后再做。**
- 这个不是必要的： add `A` record of xxx.example.com to `192.0.2.1`
- 这个描述不准确： add `xxx.example.com/*` to HTTP routes of workers  先不用操作这个，部署完成之后再处理。
- 关于 routes ：这个在部署之前先设置好。
  - libcuda.so 是你的域名, 比如说 yourdomain.com
  - 你的 routes 对应的域名，可能就是： docker.yourdomain.com   quay.yourdomain.com  gcr.yourdomain.com  k8s.yourdomain.com  等等。
- 部署的时候，会有一个配置 Custom Domains 的地方，在这里把 routes 里面配置的域名挨个填进去，每填一个就保存一个。**如果没有把域名 DNS 解析转到 cloudflare 的话，先转过来。**
- 到这里就部署完了。不需要管 域名证书，cloudflare 会自己处理。

-  配置 Docker使用的镜像地址：
```javascript
{
    "insecure-registries": [
       "https://docker.yourdomain.com"
    ],
    "registry-mirrors": ["https://docker.yourdomain.com"]
}
```
  

