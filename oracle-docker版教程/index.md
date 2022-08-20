# Oracle Docker版教程

## docker安装Oracle数据库

```dockerfile
docker pull registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
```

## Docker创建数据库

```dockerfile
docker run --name oracle_11g -d -p 1521:1521 registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
```
## 配置

1. 进入容器里

```dockerfile
docker exec -it oracle_11g bash
```

2. 配置一下环境变量，执行`su root`后，输入密码 `helowin`(数据库名字)

3. `vi /etc/profile` 并在文件最后添加如下命令

```dockerfile
export ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_2
export ORACLE_SID=helowin
export PATH=$ORACLE_HOME/bin:$PATH
```

4. 使变量生效，接着切换为 oracle用户就可以正常使用了

```dockerfile
source /etc/profile

ln -s $ORACLE_HOME/bin/sqlplus /usr/bin

su - oracle

```

5. 登录sqlplus并修改sys、system用户密码

```dockerfile
sqlplus /nolog

conn /as sysdba

alter user system identified by oracle;

alter user sys identified by oracle;

ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;

```

6. `exit`退出

7. 查看一下oracle实例状态

```dockerfile
lsnrctl status
```

8. 服务名要填写helowin(上面写的),账号:system,密码:oracle

