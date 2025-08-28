#mysql/error

# mysql会忽略配置文件

原因是配置文件的权限过于宽松，mysql不信任这个文件，将配置文件的权限设置成 `chmod 644 my.cnf` 即可
