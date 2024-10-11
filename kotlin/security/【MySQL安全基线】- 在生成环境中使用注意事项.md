
# 【MySQL安全基线】- 在生成环境中使用注意事项

在信息安全日益重要的今天，保护数据库的安全是保障系统稳定与数据安全的关键。MySQL 作为一种广泛使用的关系型数据库管理系统，存在多种安全隐患。本文将详细介绍 MySQL 的安全最佳实践，帮助用户有效降低风险。

## 1. 强化密码策略

### 弱口令
使用弱密码会极大增加被恶意猜解的风险，因此，必须立即对弱口令进行修复。

**操作步骤：**
```sql
-- 登录 MySQL 数据库
mysql -u root -p

-- 查看数据库用户及其密码信息
SELECT user, host, authentication_string FROM user; 
-- （部分版本使用的命令）
SELECT user, host, password FROM user; 

-- 修改用户密码，确保密码复杂性
SET PASSWORD FOR '用户名'@'主机' = PASSWORD('新密码');
FLUSH PRIVILEGES;
```

**密码复杂性要求：**
- 长度 8 位以上。
- 包含以下四类字符中的三类：
  - 大写字母 (A-Z)
  - 小写字母 (a-z)
  - 数字 (0-9)
  - 非字母字符 (!, $, #, %, @, ^, &)
- 避免使用已知弱口令，如 `abcd.1234`、`admin@123` 等。

### 目的
通过强密码策略，确保数据库用户的密码不易被猜测，从而降低系统被攻击的风险。

---

## 2. 修改默认端口

### 修改默认 3306 端口
使用熟知的端口会使数据库暴露于潜在的攻击中，因此应避免使用默认端口。

**操作步骤：**
1. 编辑 MySQL 配置文件 `<conf_path>/my.cnf`。
2. 在 `[mysqld]` 段落中添加或修改：
   ```ini
   port=3506
   ```
3. 重启 MySQL 服务。

### 目的
更改默认端口可以有效降低初级扫描和暴力破解的风险。

---

## 3. 禁用危险选项

### 禁用 local-infile 选项
此选项允许 MySQL 读取客户端本地文件，可能导致敏感数据泄露。

**操作步骤：**
1. 编辑 MySQL 配置文件 `<conf_path>/my.cnf`。
2. 在 `[mysqld]` 段落中添加：
   ```ini
   local-infile=0
   ```
3. 重启 MySQL 服务。

### 目的
通过禁用该选项，降低攻击者利用 SQL 注入漏洞读取敏感文件的能力。

---

### 禁用 symbolic-links 选项
禁用符号链接可以防止潜在的安全风险。

**操作步骤：**
1. 在 MySQL 配置文件 `<conf_path>/my.cnf` 中设置：
   ```ini
   symbolic-links=0
   ```
   对于 5.6 及以上版本，使用：
   ```ini
   skip_symbolic-links=yes
   ```
2. 重启 MySQL 服务。

### 目的
确保 MySQL 不会遭受通过符号链接导致的文件系统攻击。

---

## 4. 环境变量安全

### 确保 MYSQL_PWD 环境变量未设置
`MYSQL_PWD` 环境变量的使用意味着 MySQL 凭证的明文存储，极大增加凭据泄露风险。

**操作步骤：**
1. 检查环境变量：
   ```bash
   echo $MYSQL_PWD
   ```
2. 查找可能存在该变量的配置文件：
   ```bash
   grep -ir MYSQL_PWD ~/.bashrc ~/.bash_profile ~/.bash_login ~/.profile /etc/bash.bashrc /etc/profile /etc/profile.d/*
   ```

### 目的
确保 MySQL 密码不会以明文形式暴露在环境变量中，从而增强安全性。

---

## 5. 最小权限原则

### 为 MySQL 服务使用专用的最低特权账户
使用最低权限账户运行服务可以减小 MySQL 漏洞的影响。

**操作步骤：**
1. 使用非 root 用户启动 MySQL 服务。
2. 创建一个新的低权限用户并授予 MySQL 相关权限。

### 目的
限制 MySQL 服务的访问范围，确保即使出现漏洞也能减少损失。

---

## 6. 启用权限检查

### 禁止使用 --skip-grant-tables 选项
使用此选项将导致所有客户端对所有数据库具有不受限制的访问权限。

**操作步骤：**
1. 编辑 MySQL 配置文件 `<conf_path>/my.cnf`。
2. 确保未设置 `skip-grant-tables` 参数。
3. 重启 MySQL 服务。

### 目的
确保数据库的访问控制正常工作，防止未授权访问。

---

## 7. 清理无用数据库

### 删除 'test' 数据库
默认的 `test` 数据库可以供所有用户访问，可能会消耗系统资源。

**操作步骤：**
```sql
DROP DATABASE test;
FLUSH PRIVILEGES;
```

### 目的
减少攻击面，防止潜在的资源滥用。

---

## 8. 匿名账户管理

### 匿名登录检查
检查 MySQL 服务是否允许匿名登录。

**操作步骤：**
1. 检查数据库中是否存在密码为空的用户：
   ```sql
   SELECT user, host, authentication_string, plugin FROM mysql.user;
   ```
2. 为匿名账户设置密码或删除该账户：
   ```sql
   DELETE FROM mysql.user WHERE user='';
   FLUSH PRIVILEGES;
   ```

### 目的
消除匿名登录可能带来的安全隐患，确保只有合法用户能够访问数据库。

---

## 9. 版本管理

### 版本安全漏洞
以下版本存在安全漏洞，容易被入侵：

1. MySQL 5.6.35 以下和 5.7.17 以下版本存在远程安全漏洞 [详细信息](https://avd.aliyun.com/detail?id=AVD-2017-3599)。
2. 5.1.63 之前、5.5.24 之前、5.6.6 之前版本均存在身份认证绕过漏洞 [详细信息](https://avd.aliyun.com/detail?id=AVD-2012-2122)。
3. 5.5.52 及之前版本、5.6.x 至 5.6.33、5.7.x 至 5.7.15 版本均存在远程代码执行漏洞 [详细信息](https://avd.aliyun.com/detail?id=AVD-2016-6662)。

### 建议
及时升级到最新的 MySQL 版本，确保系统安全。

---

## 10. 日志管理

### 配置错误日志
启用错误日志可以帮助检测针对 MySQL 的恶意尝试。

**操作步骤：**
1. 在 MySQL 配置文件 `<conf_path>/my.cnf` 的 `[mysqld_safe]` 段落中添加：
   ```ini
   log-error=<log_path>
   ```
   例如：
   ```ini
   log-error=/var/log/mysqld.log
   ```
2. 重启 MySQL 服务。
