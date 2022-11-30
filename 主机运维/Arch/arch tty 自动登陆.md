起应是不想用lightdm/gdm之类的登录管理器  
我用的zsh，先在用户的~/.zprofile加入，如果是bash则是~/.bash_profile

```shell
if [[ -z $DISPLAY ]] && [[ $(tty) = /dev/tty1 ]]; then
	exec startx
fi
```

然后编辑你的  
**/etc/systemd/system/getty.target.wants/getty@tty1.service**

```
[Service]
# the VT is cleared by TTYVTDisallocate
# The '-o' option value tells agetty to replace 'login' arguments with an
# option to preserve environment (-p), followed by '--' for safety, and then
# the entered username.
# 原来是
ExecStart=-/sbin/agetty -a c -o '-p -- \\u' --noclear %I $TERM
# 改成，就是家一个 -a 参数，后面跟你的用户名
ExecStart=-/sbin/agetty -a c -o '-p -- \\u' --noclear %I $TERM
Type=idle
```

然后执行
```
systemctl daemon-reload


systemctl start getty@tty1.service
```

这样设置之后启动还需要输入一次密码，如果想跳过密码则  
编辑 /etc/pam.d/system-local-login
```
#%PAM-1.0

# 加入下面这一行，其中最后的 **c** 代表的是你的用户所在的用户组，一般创建用户的时候会自动创建一个同名的用户组

auth sufficient pam_succeed_if.so user ingroup c
auth      include   system-login
account   include   system-login
password  include   system-login
session   include   system-login
```