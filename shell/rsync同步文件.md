本地到远程：
```
$ rsync -av source/ username@remote_host:destination
```

远程到本地：
```
$ rsync -av username@remote_host:source/ destination
```
