# django admin 是否能支持中文 #
`LANGUAGE_CODE = 'en-us'` 改为 `LANGUAGE_CODE = 'zh-Hans'`

时区设置TIME_ZONE = 'Asia/Shanghai'

# django models 1对1关系的定义 #
一对一关系：数据库中两个表中数据的对应关系
一个账户对应着一个联系人，而一个联系人有一个账户
一对一关系是通过在两个表之间定义相同的主键来完成

    account = models.OneToOneField(Account, on_delete=models.CASCADE, primary_key=True)
    
