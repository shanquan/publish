---
layout: pagelt
title: Simplenote.py 同步备份与导出
tags: [experience]
---

（test on 10.6.78.246）

```bash
#安装simplenote
python --version
yum install -y python-setuptools-devel
easy_install simplenote
#同步notes, (含新增，修改，回收站删除，不支持永久删除)
#清空回收站（永久删除），可以使用`rm -rf /home/wwl/notes/trash/*`命令
mkdir /home/wwl/notes && cd /home/wwl
mkdir notes/trash
touch notes/.last_update
#导出文本文件
vim sync.py
#导出json
vim export.py
#执行脚本
python sync.py $username $password
python export.py $username $password
#笔记搜索关键字
#
cat notes.json | grep "text"
grep -r "text" notes/
#或者
find notes/ -type f | xargs grep "Story-2"
```
sync.py内容
```python
from simplenote import Simplenote
from decimal import Decimal
import sys
reload(sys)
sys.setdefaultencoding('utf-8')

username = sys.argv[1]
password = sys.argv[2]

simplenote = Simplenote(username, password)

noteresponse = simplenote.get_note_list()
last_update_file = open('notes/.last_update', 'r')
try:
    last_update = Decimal(last_update_file.read())
except Exception as e:
    last_update = Decimal('0')
last_update_file.close()
most_recently_updated = '0'

notes = noteresponse[0]
print "Checking %d notes..." % len(notes)
for note in notes:
    if (Decimal(note['modifydate']) > last_update):
        print '    Reading contents of %s' % note['key']
        note_data = simplenote.get_note(note['key'])
        print '    Writing contents of %s' % note['key']
	if note_data[0]['deleted'] == True:
        	filename = 'notes/trash/%s' % note['key']
	else:
		filename = 'notes/%s' % note['key']
        f = open(filename, 'w')
        f.write(note_data[0]['content'])
        f.close()
    else:
        print 'Skipped %s, no changes.' % note['key']
    if (Decimal(note['modifydate']) > Decimal(most_recently_updated)):
        most_recently_updated = note['modifydate']

last_update_file = open('notes/.last_update', 'w')
last_update_file.write(str(most_recently_updated))
last_update_file.close()

print 'Download of notes complete.'
```
export.py
```python
from simplenote import Simplenote
import sys, json
reload(sys)
sys.setdefaultencoding('utf-8')

username = sys.argv[1]
password = sys.argv[2]

simplenote = Simplenote(username, password)
noteresponse = simplenote.get_note_list()

with open('notes.json', 'w') as f:
	json.dump(noteresponse , f, indent=4 ,  ensure_ascii=False)
print 'All notes are exported to notes.json.'
```

修复非ascii编码错误：
```python
import sys
reload(sys)
sys.setdefaultencoding('utf-8')
```

CRON设置定时任务
```bash
crontab -e
#每个工作日的09~17点整点执行
0 9-17/1 * * 1-5 cd /home/wwl && python sync.py $username $password && python export.py $username $password
crontab -l
#删除任务
crontab -r
#查看任务日志
tail -f /var/log/cron
less /var/log/cron
tail -f /var/spool/mail/root
#看日志发现执行报错：TypeError: object of type 'URLError' has no len()，发现与cron环境变量有关，需设置http_proxy环境变量
```
CRON添加用户环境变量http_proxy
```
vim /var/spool/cron/root
#或crontab -e执行，在开头添加以下内容：
http_proxy=http://wang.weili:wwl!3345@10.9.26.13:8080
https_proxy=http://wang.weili:wwl!3345@10.9.26.13:8080
no_proxy=127.0.0.1,localhost,10.96.0.0/12,10.6.78.246,10.6.78.242
username=3180299xxx@qq.com
password=xx
#每小时执行
0 * * * * cd /home/simplenote && python sync.py $username $password && python export.py $username $password
```

## TODO
- [ ] 备份完成后，自动提交git历史记录，至heroku站点。
- [ ] 网页版同步异常Issue，有时候网络中断并重连后会出现登录后一片空白问题，需要清空缓存后重新登录

refs:
- [python api wrapper](https://github.com/mrtazz/simplenote.py)
- [simplenote api](https://simplenotepy.readthedocs.io/en/latest/api.html)
- [simplenote cli](https://github.com/insanum/sncli)
- [simplenote backup with a token](https://github.com/hiroshi/simplenote-backup)
- [A Python script for backing up your simplenote notes](https://gist.github.com/pklaus/32e904e39fe35b56a6333939eba1ea4c)
- <https://github.com/amussey/simplenote-to-git>
- [python中文编码-未解决](https://www.runoob.com/python/python-chinese-encoding.html)
- [终极解决方案UnicodeEncodeError: 'ascii' ](https://blog.csdn.net/u011331731/article/details/89400702)
- [crontab参考](https://www.cnblogs.com/MartinChentf/p/6060252.html)
- [less命令查看日志](https://www.cnblogs.com/lixiaochao/p/9077085.html)