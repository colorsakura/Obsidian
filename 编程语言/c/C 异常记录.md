## gets_s and fgets
在 Windows11，`gets_s` 在输入超出范围时，程序异常退出，与函数定义不符。应该用 `fgets` 代替。