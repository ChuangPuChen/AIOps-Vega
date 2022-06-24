# AIOps-Vega

#CentOS
#!/bin/sh

rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

cat > /etc/yum.repos.d/elastic.repo << EOF
[elasticsearch-7.x]
name=Elasticsearch repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
EOF
yum install epel-release -y
yum update -y
yum install elasticsearch-7.12.0 kibana-7.12.0 logstash-7.12.0 java -y
echo "network.host: 0.0.0.0" >> /etc/elasticsearch/elasticsearch.yml
echo "discovery.type: single-node"  >> /etc/elasticsearch/elasticsearch.yml
echo 'server.host: "0.0.0.0"' >>   /etc/kibana/kibana.yml

firewall-cmd --add-port=5601/tcp --permanent
firewall-cmd --add-port=9200/tcp --permanent
firewall-cmd --reload
setenforce 0

systemctl enable logstash && systemctl enable elasticsearch && systemctl enable kibana
systemctl restart logstash && systemctl restart elasticsearch && systemctl restart kibana

echo "[main]" >> /etc/yum.conf
echo "cachedir=/var/cache/yum/\$basearch/\$releasever">> /etc/yum.conf
echo "keepcache=0">> /etc/yum.conf
echo "debuglevel=2">> /etc/yum.conf
echo "logfile=/var/log/yum.log">> /etc/yum.conf
echo "exclude=kernel* elasticsearch* kibana* logstash*">> /etc/yum.conf

echo "done"
