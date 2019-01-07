
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

最后，同事说docker版本不一致，我看了一下私仓的docker是17 03，而我的docker版本是18的。

降低我的私仓版本，推送镜像成功！
