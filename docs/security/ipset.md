# ipset

ipset是iptables的扩展,它允许你创建并匹配整个地址集合的规则

## 前言

短信接口遭受大量恶意访问,需要使用封禁大量IP,如果使用iptables需要添加成千上万条规则,从而ipset应运而生.

## ipset常用命令

```bash
    ipset create ip_table hash:net maxelem 100000 #创建一个ipset，存储元素100000个；默认是65536个
    ipset destroy ip_table #删除ipset
    ipset list #查看
    ipset del  #删除
    ipset save ip_table -f /tmp/ipset_ip_table.txt #保存
    ipset restore -f /tmp/ipset_ip_table.txt #导入
    # iptables匹配ipset规则示例
    iptables -I INPUT -m set --match-set ip_table src -p tcp -m multiport --dport 80,443 -j DROP
```

## shell脚本示例

```bash
#!/usr/bin/env bash

log_dir='/data/logs/www'
log_file="$1"
yesterday=$(date -d '-1 day' "+%Y%m%d")
tmp_ip_table="/tmp/access_ip_table.log"
export_ip_table="/tmp/ipset_ip_table.txt"
write_ip_table="/tmp/write_ip_table.txt"
#limit_count=10
rule_ip="(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)"

[ -z "$log_file" ] && log_file="$log_dir/wechat.simpletour.com.access.log_$yesterday"
#grep "403" $log_file |egrep -o "([0-9]{1,3}[\.]){3}[0-9]{1,3}" |sort |uniq -c |sort -nr |awk '{if ($1 > "'$limit_count'") print $2"---"$1 }'
egrep "/mobile/gateway/wechat/account/regist|/mobile/gateway/wechat/account/sendverifycode/regist HTTP/1.1" $log_file \
    |egrep -o $rule_ip |sort |uniq -c |sort -nr |awk '{if ($1 > 10) print $2}' > $tmp_ip_table

# save ipset ip table
ipset save ip_table -f $export_ip_table

# clean $write_ip_table
[ -s $write_ip_table ] && > $write_ip_table

while read LINE;do
    grep -q $LINE $export_ip_table
    # 
    [ $? -eq 0 ] || echo "add ip_table $LINE" >> $write_ip_table 
done < $tmp_ip_table

ipset restore -f $write_ip_table
# add iptables rule
# iptables -I INPUT -m set --match-set ip_table src -p tcp -m multiport --dport 80,443 -j DROP
#
# iptables -L -n --line-numbers
# iptables -D INPUT 2
```

## 参考

**[官网](http://ipset.netfilter.org/)**

> 记录时间: 2018年 04月 18日 星期三 16:23:03 CST