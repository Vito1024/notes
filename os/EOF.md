### What is EOF
	- End Of File.  
### 
EOF 其实不是一个字符，CTRL-D在Linux中会被终端解释为INTERRUPT信号，从而终止了read系统调用，起到了类似EOF的作用。
在读文件时，数据在磁盘里面是以utf8编码后紧凑存放起来的，如果read调用拿到的磁盘数据为0时，就认为到了文件的末尾，返回EOF。
