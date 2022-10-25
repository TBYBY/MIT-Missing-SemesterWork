# Linux 中的用户权限问题
## 1. 基本概念
用户组：

    由于Linux对多用户管理繁琐，所以使用组进行管理就比较简单。
    可以通过 groupadd groupName 将用户加入指定用户组中。

用户权限：

     权限    | 对文件的影响   | 对目录的影响
    ----------------------------------------
    r（读取）  可读取文件内容   可列出目录内容
    w（写入）  可修改文件内容   可在目录中创建删除内容
    x（执行）  可作为命令执行   可访问目录内容
    
    Linux 的授权则是默认给三种角色：user / group / other
    每个文件的权限基于以上 UGO 进行设置。
    drwxrwxr-x  2 peter1 peter1 4096 Dec 10 01:36 peter.net
    d -- 表示目录, - 表示普通文件
    rwx : 表示 User 有 读写/运行权限
    rwx ；表示所在用户组有 读写/运行权限
    r-x : 表示 Other 其他组用户只有 读和运行权限，不允许修改
    2 ： 表示其下的子目录数
    peter1 ； User 的 Name
    peter1 : 表示用户组
    4096：文件大小
    Dec ... : 表示最后修改时间
    peter.net : 为文件名
## 2. 用户权限修改 chmod

    chmod -R ： 对当前目录下的所有文件与子目录进行相同的权限变更
    Exmaple:
    chmod -R u+rwx peter.net // 为当前用户增加 rwx 权限
    chmod -R g+rwx peter.net // 为当前用户组增加 rwx 权限
    chmod -R u+rwx,g+rwx,o_rwx peter.net // 为当前用户，用户组以及其他人增加 rwx 权限

    chmod -R u = rx, g = rx, o = rx peter.net // 对 ugo 只有 rx权限
    chmod -R u-x peter.net // 撤销 u 的运行权限

## 3. 权限的二进制表示
    
    有权限表示 1, 无权限表示 0。
    rwx = 111 = 7 // r = 4, w = 2, x= 1
    r-x = 101 = 5
    rw- = 110 = 6
    r-- = 100 = 4;
    可以依据以上对用户进行权限设置。