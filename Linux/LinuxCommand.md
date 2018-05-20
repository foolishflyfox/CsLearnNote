---
export_on_save:
 html: true
---
@import "/blog_head.md"

# Linux 命令备忘


- vi命令
    - 命令模式
        - `:5,10 w! new.txt` : 将该文件中的第5行到第10行写入到 `new.txt` 文件中
<br>
- scp 文件传输 : `scp [-P 22] filename username@192.168.1.100:~/dirname`
<br>

- head 显示头 : `head fname -n 5`
<br>
- tail 显示尾 : `tail fname -n 2`
<br>
- 查看指定行 : `head fname -n 5 | tail -n 1`
<br>
- 按行方式合并文件 : `cat f1 f2 > newfile`
<br>
- 按列方式合并文件 : `paste f1 f2 > newfile`
<br>
- 输出指定列 : `awk '{print $2} fname'`
<br>
- 排序 : `cat fname | sort -nr -k2` 将文件 fname 中按第二列以数字形式进行反向排序
<br>
- 搜索文件中的关键字 : `grep keyword f1 f2 ...`
<br>
- 搜索关键字在哪些文件中出现过 : `grep -l keyword *`
<br>
- 统计模式出现在多少行中 : `grep -c keyword f1 f2 ...`
<br>
- 正则表达式搜索 : `grep -E '^a.*c$' f1 f2`
<br>
- 忽略大小写 : `grep -i 'a'`
<br>
- 获取捕捉行 : `grep -oe '[0-9]*' f1 f2 ...`
<br>
- 反向选择行 : `grep -v keyword f1 f2 ...` 
<br>
- 执行历史命令 : `!!` 执行上一条命令，`!123` 执行编号为123的历史命令
<br>
- 搜索当前目录下含关键字的所有文件，并显示关键字所在行 : `grep "Keyword" . -Rn`

