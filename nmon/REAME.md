##nmon系统监控工具
nmon_linux_14i.tar.gz：安装包
nmon_analyser_v51_2.zip：分析器

mv XXX /usr/local/bin/nmon
##每2秒收集一次，收集100次，生成的文件放到/home/syj下
nmon -s2 -c100 0f /home/syj/
