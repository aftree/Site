# nginx访问日志分析

_access.log_

```text
58.35.39.71 - - [24/Aug/2018:14:30:51 +0800] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36" "-"
58.35.39.71 - - [24/Aug/2018:14:31:33 +0800] "GET / HTTP/1.1" 200 3700 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/11.1.2 Safari/605.1.15" "-"
```

nginx 访问量统计

```bash
PV(访问量)：即Page View, 即页面浏览量或点击量，用户每次刷新即被计算一次。
UV(独立访客)：即Unique Visitor,访问您网站的一台电脑客户端为一个访客。00:00-24:00内相同的客户端只被计算一次。
IP(独立IP)：即Internet Protocol,指独立IP数。00:00-24:00内相同IP地址之被计算一次。

1.根据访问IP统计UV
awk '{print $1}'  access.log|sort | uniq -c |wc -l

2.统计访问URL统计PV
awk '{print $7}' access.log|wc -l

3.查询访问最频繁的URL
awk '{print $7}' access.log|sort | uniq -c |sort -n -k 1 -r|more

4.查询访问最频繁的IP
awk '{print $1}' access.log|sort | uniq -c |sort -n -k 1 -r|more

5.根据时间段统计查看日志
cat  access.log| sed -n '/14\/Mar\/2015:21/,/14\/Mar\/2015:22/p'|more

6.统计IP访问个数（和根据访问IP统计UV一样）
cat access.log | awk '{ips[$1]+=1} END{for(ip in ips) print ips[ip],ip}' | sort -nr | wc -l

7.查看3点-6点之间的Ip访问个数
grep "2016:0[3-6]" access.log | awk '{ips[$1]+=1} END{for(ip in ips) print ips[ip],ip}' | sort –nr | wc -l

8.查看3点-6点之间的ip访问数，并且访问数>=200的ip.
grep '2016:0[3-6]' access.log | awk '{ips[$1]+=1}END{for(ip in ips) if(ips[ip]>=200) print ips[ip],ip}' | sort -nr

9.查看并发连接数
netstat -nat|grep ESTABLISHED|wc -l

10.获取每分钟的请求数量，输出成csv文件
cat /usr/local/nginx/logs/access.log  | awk '{print substr($4,14,5)}' | uniq -c | awk '{print $2","$1}' > access.csv

11.获取最耗时的请求时间、url、耗时，前10名, 可以修改后面的数字获取更多，不加则获取全部。
cat /usr/local/nginx/logs/access.log | awk '{print $4,$7,$NF}' | awk -F '"' '{print $1,$2,$3}' | sort -k3 -rn | head -10
```

