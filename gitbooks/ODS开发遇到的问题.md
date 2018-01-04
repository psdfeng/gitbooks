# 开始做之前，先把思路整理好，把遇到的问题在这里记录，记录解决的办法。 #

1. # django admin 是否能支持中文 #
`LANGUAGE_CODE = 'en-us'` 改为 `LANGUAGE_CODE = 'zh-Hans'`

时区设置TIME_ZONE = 'Asia/Shanghai'

# django models 1对1关系的定义 #
一对一关系：数据库中两个表中数据的对应关系
一个账户对应着一个联系人，而一个联系人有一个账户
一对一关系是通过在两个表之间定义相同的主键来完成
在主表中添加字段映射到字表中，同时删除原来的migration，重新makemigrations app  

    account = models.OneToOneField(Account, on_delete=models.CASCADE, primary_key=True)

# django 模型的两个重要命令 #
$ python manage.py makemigrations TestModel  # 让 Django 知道我们在我们的模型有一些变更
$ python manage.py migrate TestModel   # 创建表结构


# 修改了model重新生成model的办法 #
1. 删除表格数据django_migrations  
2. migrate --fake-initial [app]  

# 在django中注册model时，如果默认注册所有字段可以简写为： #
```
class RankAdmin(admin.ModelAdmin):  
	model = Rank
```
#2018-01-04 user model的test 测试思路


user表的测试，主要测试was_user_start_not_end()

1. user没有userStDatetime
2. user没有userStDatetime未到当前时间
2. user有userStDatetime 没有 userEndDatetime
3. user有userStDatetime y有 userEndDatetime 未到期，
4. user有userStDatetime y有 userEndDatetime 到期，

遇到问题，执行python manage.py test 报Empty test suite.


