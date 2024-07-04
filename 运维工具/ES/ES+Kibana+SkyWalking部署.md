1、部署ES
docker run --name es8.5.0_1 --net elastic-net -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e CLI_JAVA_OPTS="-Xms1024m -Xmx1024m" -v esplugins:/usr/share/elasticsearch/plugins -v esdata:/usr/share/elasticsearch/data -v esconfig:/usr/share/elasticsearch/config -it elasticsearch:8.5.0
或
docker run --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx1024m"  -d elasticsearch:8.5.0
 
2、生成超级管理员elastic的密码，kibana可以登录
docker exec -it elasticsearch /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic

3、部署kibana
docker run --link elasticsearch -d --name kibana -p 5601:5601  kibana:8.5.0

4、生成enrollment-token（目前只发现kibana登录需要使用）
docker exec -it elasticsearch elasticsearch-create-enrollment-token -s kibana

5、创建Code
docker exec -it kibana kibana-verification-code


6、SkyWalking部署
6.1、Skywalking OAP部署
docker run --name skywalking-oap --restart always -p 11800:11800 -p 12800:12800  -p 1234:1234 -d --privileged=true -e TZ=Asia/Shanghai -e SW_STORAGE=elasticsearch8 -e SW_STORAGE_ES_CLUSTER_NODES=192.168.40.117:9200 -v /etc/localtime:/etc/localtime:ro apache/skywalking-oap-server:9.2.0
 
6.2、Skywalking UI部署
docker run -d --name skywalking-ui --restart always -p 8081:8080 --link skywalking-oap:skywalking-oap -e TZ=Asia/Shanghai -e SW_OAP_ADDRESS=[http://192.168.40.117:12800](http://192.168.40.117:12800) -v /etc/localtime:/etc/localtime:ro apache/skywalking-ui:9.2.0


 
