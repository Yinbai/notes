Docker

1. 安装

   https://get.daocloud.io/#install-docker-for-mac-windows

2. 修改镜像地址

   在 Registry mirrors 中添加：https://registry.docker-cn.com

3. 更新版本

4. 在新版本中添加镜像地址

   ```json
   # Docker Engine
   {
     "debug": true,
     "experimental": false,
     "registry-mirrors": [
       "https://registry.docker-cn.com",
       "https://docker.mirrors.ustc.edu.cn", // 中国某高等大学的
       "https://hub-mirror.c.163.com" // 网易的
     ]
   }
   ```

5. 文档

   https://docs.docker.com/get-started/

6. 6