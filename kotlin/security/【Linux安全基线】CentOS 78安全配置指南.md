### 【Linux安全基线】- CentOS 7/8安全配置指南

在企业业务的生产环境中，Linux服务器的安全性至关重要，尤其是对于具有超级用户权限的`root`账号。滥用或被入侵后，可能会造成数据泄露、系统损坏等严重安全问题。为了减少这种风险，本文将详细介绍如何通过一系列安全措施来增强CentOS 7/8服务器的安全性，降低系统被攻击的风险。

#### 1. 创建普通用户并禁用Root用户直接登录

**场景说明：** 在Linux系统中，root用户拥有最高权限，允许其对系统进行任何操作。然而，root账户的滥用或者由于弱密码被破解，可能会导致系统被完全控制。因此建议创建普通用户并为其赋予管理权限，而非直接使用root账户。

**操作步骤：**

- 创建新用户：`adduser ecs-user`

- 设置用户密码：`passwd ecs-user`

- 确认账户可正常登录：通过新的`ecs-user`账户登录系统，确保一切正常。

- 为新用户添加免密sudo权限：

  ```shell
  vim /etc/sudoers
  ```

  ，在

  ```shell
  root ALL=(ALL) ALL
  ```

  下添加：

  ```shell
  ecs-user ALL=(ALL) NOPASSWD:ALL
  ```

- 禁止root登录：编辑SSH配置文件`/etc/ssh/sshd_config`，找到`PermitRootLogin`项，将其设置为`PermitRootLogin no`，如果不存在此项，手动添加。

**解释：** 通过禁用root直接登录，可以避免因root账户被入侵导致的系统完全失控。此外，普通用户通过sudo提权执行管理操作，增加了一层安全保护。

#### 2. 设置SSH空闲超时退出

**场景说明：** 在业务运维过程中，如果管理人员通过SSH远程登录服务器并长时间未操作，保持会话活跃可能会导致未授权人员接管该会话。设置超时退出机制可以有效降低这种风险。

**操作步骤：**

- 编辑SSH配置文件

  ```shell
  /etc/ssh/sshd_config
  ```

  ，设置：

  ```shell
  ClientAliveInterval 600
  ClientAliveCountMax 2
  ```

**解释：** 这意味着系统会在10分钟（600秒）内没有收到客户端响应时发起两次探测（ClientAliveCountMax），如果依然没有回应，会自动断开连接，避免空闲会话带来的风险。

#### 3. 设置密码修改最小间隔时间

**场景说明：** 如果允许用户频繁更改密码，可能会导致弱密码的重用，或形成恶意行为，例如通过多次快速更改绕过系统的密码策略。因此需要设置密码修改的最小间隔时间。

**操作步骤：**

- 编辑文件

  ```shell
  /etc/login.defs
  ```

  ，设置：

  ```shell
  PASS_MIN_DAYS 7
  ```

- 为root用户设置相同策略：

  ```shell
  chage --mindays 7 root
  ```

**解释：** 该设置确保用户在7天内无法更改密码，从而防止通过频繁更改密码试图绕过系统安全策略。

#### 4. 设置密码失效时间

**场景说明：** 密码长期不修改会增加密码被暴力破解或泄露的风险。设置密码失效时间可以强制用户定期更换密码，提升系统安全性。

**操作步骤：**

- 编辑文件

  ```shell
  /etc/login.defs
  ```

  ，设置：

  ```shell
  PASS_MAX_DAYS 90
  ```

- 为root用户设置相同策略：

  ```shell
  chage --maxdays 90 root
  ```

**解释：** 密码失效时间为90天，超过这个时间段，用户必须更改密码才能继续使用系统。这一措施可以减少密码泄露或被猜测的风险。

#### 5. 限制密码重用

**场景说明：** 限制用户重复使用以前的密码可以降低由于暴力破解导致的安全威胁。强制用户使用全新的密码有助于提升整体系统安全性。

**操作步骤：**

- 编辑文件

  ```shell
  /etc/pam.d/password-auth
  ```

  和

  ```shell
  /etc/pam.d/system-auth
  ```

  ，在

  ```shell
  password sufficient pam_unix.so
  ```

  后面添加：

  ```shell
  remember=5
  ```

**解释：** 该配置强制用户至少使用5次不同的密码后才能重新使用某个旧密码，防止密码重复使用造成的安全隐患。

#### 6. 密码复杂度检查

**场景说明：** 密码复杂度的提高能够有效防止弱密码的使用，从而增强系统的安全性。通过强制要求密码使用多种字符类型，系统可以有效抵御暴力破解等攻击。

**操作步骤：**

- 编辑文件

  ```shell
  /etc/security/pwquality.conf
  ```

  ，设置：

  ```shell
  minlen=10
  minclass=3
  ```

**解释：** `minlen=10`要求密码长度至少为10个字符，`minclass=3`表示密码中至少要包含三种不同类型的字符（如大写、小写、数字、特殊符号等），从而增强密码强度。

#### 7. 禁止SSH空密码用户登录

**场景说明：** 允许用户使用空密码登录SSH会大大降低系统的安全性，容易被恶意攻击者利用。因此需要禁止使用空密码登录。

**操作步骤：**

- 编辑

  ```shell
  /etc/ssh/sshd_config
  ```

  ，设置：

  ```shell
  PermitEmptyPasswords no
  ```

**解释：** 禁用空密码用户登录SSH，确保所有用户账户都有设置密码，以此提高系统的安全性。

#### 8. SSH登录最大尝试次数

**场景说明：** 攻击者通过暴力破解尝试多次错误登录有可能最终获得系统访问权限。限制最大登录尝试次数可以有效防止此类攻击。

**操作步骤：**

- 编辑文件

  ```shell
  /etc/ssh/sshd_config
  ```

  ，设置：

  ```shell
  MaxAuthTries 4
  ```

**解释：** 该设置表示用户在SSH登录时最多可尝试4次密码输入错误，超过4次系统将自动断开连接，防止暴力破解。

#### 9. 确保rsyslog服务已启用

**场景说明：** `rsyslog`服务用于收集并记录系统日志，帮助运维人员进行审计和故障排查。确保该服务的正常运行对于安全审计至关重要。

**操作步骤：**

- 启动并启用

  ```shell
  rsyslog
  ```

  服务：

  ```shell
  systemctl enable rsyslog
  systemctl start rsyslog
  ```

**解释：** 确保日志记录服务启动，防止系统事件未被记录，影响问题排查和安全审计。

#### 10. 配置文件权限设置

**场景说明：** 配置文件如果被非授权用户访问或修改，可能会导致系统被攻破或配置信息泄露。确保关键配置文件仅可被root用户访问可以提升系统安全性。

**操作步骤：**

- 设置权限：

  ```shell
  chown root:root /etc/hosts.allow
  chown root:root /etc/hosts.deny
  chmod 644 /etc/hosts.allow
  chmod 644 /etc/hosts.deny
  ```

**解释：** `chown`确保这些配置文件的拥有者是root用户，`chmod 644`确保只有root用户拥有写权限，其他用户只能读取，防止配置文件被篡改。

#### 11. 设置用户权限文件的权限

**场景说明：** 系统中的`passwd`、`shadow`、`group`等文件存储了用户的敏感信息（如密码等）。设置适当的文件权限可以防止非授权用户访问这些信息。

**操作步骤：**

- 执行以下命令：

  ```shell
  chown root:root /etc/passwd /etc/shadow /etc/group /etc/gshadow
  chmod 0644 /etc/passwd /etc/group
  chmod 0400 /etc/shadow /etc/gshadow
  ```

**解释：** 这些命令确保只有root用户可以修改这些文件，并且限制其他用户的访问权限。

#### 12. 确保root是唯一UID为0的账户

**场景说明：** `UID`为0的用户拥有系统的最高权限，通常只有root账户拥有此权限。确保没有其他用户拥有`UID=0`可以避免权限被滥用。

**操作步骤：**

- 检查系统中是否存在UID为0的用户：

  ```shell
  cat /etc/passwd | awk -F: '($3 == 0) { print $1 }' | grep -v '^root$'
  ```

**解释：** 如果出现非root的UID为0用户，建议立即删除或更改其UID，防止权限滥用。

#### 13. 密码到期警告天数

**场景说明：** 提前通知用户密码即将过期，提醒用户及时更改密码可以减少因密码过期导致的账户无法访问问题。

**操作步骤：**

- 编辑

  ```shell
  /etc/login.defs
  ```

  文件，设置：

  ```shell
  PASS_WARN_AGE 7
  ```

- 为root用户设置相同策略：

  ```shell
  chage --warndays 7 root
  ```

**解释：** 密码到期前7天会提前发出警告，用户可以及时更改密码，确保账户的可用性和安全性。

#### 14. SSH日志级别设置为INFO

**场景说明：** `LogLevel`设置为INFO可以记录更多有用的信息，便于管理员在出现问题时快速排查问题。

**操作步骤：**

- 编辑

  ```shell
  /etc/ssh/sshd_config
  ```

  ，设置：

  ```shell
  LogLevel INFO
  ```

**解释：** 日志级别设置为INFO可以记录登录、认证失败等关键信息，提升安全审计能力。

#### 15. 开启地址空间布局随机化（ASLR）

**场景说明：** 地址空间布局随机化（ASLR）通过随机化进程的内存地址布局来增加攻击者利用漏洞攻击的难度，从而提高系统的安全性。

**操作步骤：**

- 编辑

  ```shell
  /etc/sysctl.conf
  ```

  或

  ```shell
  /etc/sysctl.d/
  ```

  文件，添加以下内容：

  ```shell
  kernel.randomize_va_space = 2
  ```

- 立即生效：

  ```shell
  sysctl -w kernel.randomize_va_space=2
  ```
