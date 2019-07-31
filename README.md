* 파일 리스트

1. docker-compose.yml
 elastic search - master node
 elastic search - data node
 kibana 

2. log 를 쌓기 위한 nginx webserver 구동
 2-1. nginx.conf
    - log_format 을 설정하여, 필요한 정보를 access log 에 쌓음
 2-2. Dockerfile_nginx
    - customizing 한 nginx.conf 파일로 docker image 를 만듬
 
3. access log -> redis 에 넣기위해 logstash 로 파이프라인 구성
 3-1. logstash.conf
    - 파이프 라인의 input 정보를 설정, path, type, codec, filter / output 구성
      nginx 의 access 로그를 구성하여 redis 에 넘김
 3-2. Dockerfile_logstash
    - customizing 한 logstash.conf 파일로 docker image 를 만듬
    
4. redis 실행

5. logstash.conf
