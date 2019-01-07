
安装docker 17 03版本的时候出现异常

```
[root@walker ~]#  yum install docker-ce-17.03.2.ce-1.el7.centos
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.lzu.edu.cn
 * extras: mirror.lzu.edu.cn
 * updates: mirror.lzu.edu.cn
Resolving Dependencies
--> Running transaction check
---> Package docker-ce.x86_64 0:17.03.2.ce-1.el7.centos will be installed
--> Processing Dependency: docker-ce-selinux >= 17.03.2.ce-1.el7.centos for package: docker-ce-17.03.2.ce-1.el7.centos.x86_64
Package docker-ce-selinux is obsoleted by docker-ce-cli, but obsoleting package does not provide for requirements
--> Processing Dependency: libcgroup for package: docker-ce-17.03.2.ce-1.el7.centos.x86_64
--> Processing Dependency: libltdl.so.7()(64bit) for package: docker-ce-17.03.2.ce-1.el7.centos.x86_64
--> Running transaction check
---> Package docker-ce.x86_64 0:17.03.2.ce-1.el7.centos will be installed
--> Processing Dependency: docker-ce-selinux >= 17.03.2.ce-1.el7.centos for package: docker-ce-17.03.2.ce-1.el7.centos.x86_64
Package docker-ce-selinux is obsoleted by docker-ce-cli, but obsoleting package does not provide for requirements
---> Package libcgroup.x86_64 0:0.41-20.el7 will be installed
---> Package libtool-ltdl.x86_64 0:2.4.2-22.el7_3 will be installed
--> Finished Dependency Resolution
Error: Package: docker-ce-17.03.2.ce-1.el7.centos.x86_64 (docker-ce-stable)
           Requires: docker-ce-selinux >= 17.03.2.ce-1.el7.centos
           Available: docker-ce-selinux-17.03.0.ce-1.el7.centos.noarch (docker-ce-stable)
               docker-ce-selinux = 17.03.0.ce-1.el7.centos
           Available: docker-ce-selinux-17.03.1.ce-1.el7.centos.noarch (docker-ce-stable)
               docker-ce-selinux = 17.03.1.ce-1.el7.centos
           Available: docker-ce-selinux-17.03.2.ce-1.el7.centos.noarch (docker-ce-stable)
               docker-ce-selinux = 17.03.2.ce-1.el7.centos
           Available: docker-ce-selinux-17.03.3.ce-1.el7.noarch (docker-ce-stable)
               docker-ce-selinux = 17.03.3.ce-1.el7
 You could try using --skip-broken to work around the problem
 You could try running: rpm -Va --nofiles --nodigest
```

解决方案：
https://github.com/moby/moby/issues/33930

