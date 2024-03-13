# cgit

Cgit 是一个git仓库的前端服务, 使用C编写. 内核的仓库就是用的这个.

note: 将 cgit 仓库所有者设置为 `git:www-data`, 需要检查文件夹
用户组的读写权限, 如果没有写权限的话, git push 的时候会发生
`error: remote unpack failed: unable to create temporary object directory` 的错误.
