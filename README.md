# spring-boot-mybatis-mysql-write-read
springboot 学习mybatis下mysql的读写分离

实现数据库的主从复制，保证两个数据库的数据一致。
1.主数据库：找到MySQL安装目录下的my.ini，编辑此文件，在mysqld节点下添加
server-id=1
#启动同步事件的日志记录文件  
log-bin="C:/Program Files (x86)/MySQL/MySQL Server 5.5/data"
重启MySQL服务，打开MYSQL命令窗口，以root身份登入，
查看二进制日志启动情况
show variables like '%log_bin%';
进行授权
grant replication slave on *.* to slave@127.0.0.1 identified by 'slave' ;
刷新机制
flush privileges;
查看主机状态
show master status;
127.0.0.1表示本地主机，slave表示给从机设置一个读取二进制日志的账号，by 'slave'表示slave账号的密码。
2.从机找到MySQL安装目录下的my.ini，编辑此文件，在mysqld节点下添加
server-id=3
重启MySQL服务，打开MYSQL命令窗口，以root身份登入，
change master to master_host='127.0.0.1',master_port=3307,master_user='slave',master_password='slave', master_log_file='data.000003',master_log_pos=1055;
master_host：主机的ip地址，
master_port：主机的端口号，默认是3306，如果修改了端口，则需要设置端口号，否则会找不到主机的二进制日志，
master_user：主机创建的slave账号
master_password：主机创建的slave账号的密码，
master_log_file：show master status时的信息，
master_log_pos：show master status时的信息。
启动从机
start slave;
查看从机状态
show slave status\G;
当出现Slave_IO_Running: Yes   Slave_SQL_Running: Yes则表示成功了。
注意：在安装两个MySQL服务的时候需要设置不同的端口及不同的服务名，否则会产生端口及服务冲突的。