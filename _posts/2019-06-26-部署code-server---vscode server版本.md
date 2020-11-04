
### 部署方式1
```
sudo wget https://github.com/cdr/code-server/releases/download/1.1119-vsc1.33.1/code-server1.1119-vsc1.33.1-linux-x64.tar.gz
tar -zxvf code-server1.1119-vsc1.33.1-linux-x64.tar.gz
cd code-server1.1119-vsc1.33.1-linux-x64/
## run code-server
## open https://localhost:8443/
## local password: dcbebe75030fb209456955d9
 ./code-server
 
```

### 通过docker快速部署

> 当然必须保证你的服务器上已经安装了docker

```
docker run -it -d -p 8443:8443  codercom/code-server --allow-http --no-auth
```
