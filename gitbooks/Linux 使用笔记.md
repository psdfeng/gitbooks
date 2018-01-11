


# 1. 卸载程序和所有配置文件。在终端中输入以下命令，把<programname>替换成你希望完全移除的程序： #

sudo apt-get --purge remove <programname>

# 2. 只卸载程序。如果你移除程序但保留配置文件，请输入以下命令： #

sudo apt-get remove <programname>

# 3. linux下把普通用户添加到sudo组的方式。  #
1. 先cd到/etc/sudoers目录下 
2. 由于sudoers文件为只读权限，所以需要添加写入权限，chmod u+w sudoers 
3. vim sudoers 
4. 找到root ALL = (ALL) ALL这一行，在下一行加入username ALL = (ALL) ALL。username指代你想加入sudo组的用户名。 
5. 把sudoers文件的权限修改回来。chmod u-w sudoers 
6. 这样普通用户可以执行sudo命令了。