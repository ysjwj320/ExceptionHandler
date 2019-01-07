
docker推送镜像到私有仓时，出现TLS证书验证失败

服务端报错

```
http: TLS handshake error from 117.175.169.132:50994: remote error: tls: bad certificate
```

客户端报错

```
x509: certificate is not valid for any names, but wanted to match docker.smart-china.com
```

遇到这个问题后，第一想法就是证书错误，可是验证证书MD5值，都是一样的。

第二点，验证push时，证书是否生效，通过抓包看ca信息都是携带到TLS里面的。

最后，同事说docker版本不一致，我看了一下私仓的docker是17 03，而我的docker版本是18 06的。

降低我的私仓版本，推送镜像成功！

验证：私仓：docker 18 06，客户端：docker 17 03，推送同样不行，报错如下：

```
The push refers to a repository [docker-hub.***.cn:5000/nginx]
Get https://docker-hub.***:5000/v1/_ping: x509: certificate signed by unknown authority (possibly because of "x509: invalid signature: parent certificate cannot sign this kind of certificate" while trying to verify candidate authority certificate "docker-hub.***.cn")

```
