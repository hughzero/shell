#!/bin/bash

#脚本功能描述:监控服务器主要性能参数指标
#监控项目:内核信息,主机名称,IP地址,登录账户,内存与swap信息,磁盘信息,CPU负载
#内核信息
kernel=$(uname -r)

#操作系统版本
release=$(cat /etc/redhat-release)

#主机名称
hostname=$HOSTNAME

#本地IP地址列表
localip=$(ip a s | awk '/inet /{print $2}')

#总内存容量
mem_total=$(free | awk '/Mem/{print $2}')

#剩余内存容量
mem_free=$(free | awk '/Mem/{print $NF}')

#总swap容量
swap_total=$(free | awk '/Swap/{print $2}')

#剩余swap容量
swap_free=$(free | awk '/Swap/{print $NF}')

#磁盘信息
disk=$(df | awk '/^\/dev/{print $1,$2,$4}'|column -t)

#CPU最近1分钟的平均负载
load1=$(uptime | sed 's/,//g' | awk '{print $(NF-2)}')

#CPU最近5分钟的平均负载
load5=$(uptime | sed 's/,//g' | awk '{print $(NF-1)}')

#CPU最近15分钟的平均负载
load15=$(uptime | sed 's/,//g' | awk '{print $(NF)}')

#登录用户数量
login_users=$(who | wc -l)

#进程数量
procs=$(ps aux | wc -l)

#系统总账户数量
users=$(sed -n '$=' /etc/passwd)

#CPU型号
cpu_info=$(LANG=C lscpu |awk -F: '/Model name/ {print $2}')

#CPU内核数量
cpu_core=$(awk '/processor/{core++} END{print core}' /proc/cpuinfo)

#安装性能监控软件
yum -y -q  install sysstat &>/dev/null

echo -e "\033[34m提取磁盘性能指标,请稍后...\033[0m"

tps=$(LANG=C sar -d -p 1 6 | awk '/Average/' | tail -n +2 |awk '{print "["$2"]磁盘平均IO数量:"$3}') &

read_write=$(LANG=C sar -d -p 1 6 |awk '/Average/' | tail -n +2 | awk '{print "["$2"]平均每秒读写扇区量:"$4,$5}') &

#中断数量
irq=$(vmstat 1 2 |tail -n +4 | awk '{print $11}')

#上下文切换数量
cs=$(vmstat 1 2|tail -n +4 | awk '{print $12}')

#占用内存资源最多的10个进程列表
top_proc_mem=$(ps --no-headers -eo comm,rss | sort -k2 -n |tail -10)

#占用内存资源最多的10个进程列表
top_proc_cpu=$(ps --no-headers -eo comm,rss | sort -k2 -n |tail -10)

#获取网卡流量信息,接收|发送的数据流量,单位为字节(bytes)
net_monitor=$(cat /proc/net/dev | tail -n +3 |awk 'BEGIN{ print "网卡名称 入站数据流量(bytes) 出站数据流量(bytes)"} {print $1,$2,$10}' | column -t)

#输出数据信息
echo -e "\033[32m************************本机主要参数列表********************\033[0m"

echo -e "本机IP地址列表:\033[32m$localip\033[0m"

echo -e "本机主机名称:\033[32m$hostname\033[0m"

echo -e "操作系统版本为:\033[32m$release\033[0m,内核版本:\033[32m$kernel\033[0m"

echo -e "CPU型号为:\033[32m$cpu_info\033[0m,CPU内核数量:\033[32$kernel\033[0m"

echo -e "本机总内存容量:\033[32m$mem_total\033[0m,剩余可用内存容量:\033[32m$mem_free\033[0m"

echo -e "本机swap总容量:\033[32m$swap_local\033[0m,剩余swap容量:\033[32m$swap_free\033[0m"

echo -e "CPU最近1分钟,5分钟,15分钟的平均负载分别为:\033[32m$load1 $load5 $load15\033[0m"

echo -e "本机总账户数量为:\033[32m$users\033[0m,当前登录系统的账户数量:\033[32m$login_users\033[0m"

echo -e "当前系统中启动的进程数量:\033[32m$procs\033[0m"

echo -e "占用CPU资源最多的10个进程列表为:"

echo -e "\033[32m$top_proc_cpu\033[0m"

echo -e "占用CPU内存资源最多的10个进程列表为:"

echo -e "\033[32m$top_proc_mem\033[0m"

echo -e "CPU中断数量为:\033[32m$irq\033[0m,CPU上下文切换数量:\033[32m$cs\033[0m"

echo -e "每个磁盘分区的总容量与剩余容量信息如下:"

echo -e "$disk"

echo -e "$tps"

echo -e "$read_write"

echo -e "$net_monitor"

echo -e "\033[32m************************巡检结束********************\033[0m"
