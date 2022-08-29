创建一个dockerimage.sh
```
#!/bin/bash

if [ $# -lt 1 ]
then
cat << HELP

dockertags  --  list all tags for a Docker image on a remote registry.

EXAMPLE:
    - list all tags for ubuntu:
       dockertags ubuntu

    - list all php tags containing apache:
       dockertags php apache

HELP
fi

image="$1"
tags=`wget -q https://registry.hub.docker.com/v1/repositories/${image}/tags -O -  | sed -e 's/[][]//g' -e 's/"//g' -e 's/ //g' | tr '}' '\n'  | awk -F: '{print $3}'`

if [ -n "$2" ]
then
    tags=` echo "${tags}" | grep "$2" `
fi

echo "${tags}"
```

加上权限：
```
chmod +x dockersearch.sh
```

实战：
```
./dockersearch.sh centos
latest
5
5.11
6
6.10
6.6
6.7
6.8
6.9
7
7.0.1406
7.1.1503
7.2.1511
7.3.1611
7.4.1708
7.5.1804
7.6.1810
7.7.1908
7.8.2003
7.9.2009
8
8.1.1911
8.2.2004
8.3.2011
8.4.2105
centos5
centos5.11
centos6
centos6.10
centos6.6
centos6.7
centos6.8
centos6.9
centos7
centos7.0.1406
centos7.1.1503
centos7.2.1511
centos7.3.1611
centos7.4.1708
centos7.5.1804
centos7.6.1810
centos7.7.1908
centos7.8.2003
centos7.9.2009
centos8
centos8.1.1911
centos8.2.2004
centos8.3.2011
centos8.4.2105
```
