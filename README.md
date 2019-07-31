1. docker-compose.yml
 elastic search - master node
 elastic search - data node
 kibana 

2. log 를 쌓기 위한 nginx webserver 구동
 2-1. nginx.conf
    : log_format 을 설정하여, 필요한 정보를 access log 에 쌓음
 
 2-2. Dockerfile_nginx
    : customizing 한 nginx.conf 파일로 docker image 를 생성
    : build command : docker build -t nginx-elk -f ./Dockerfile_nginx .
    : run command : docker run --name nginx-elk -p 80:80 -d -v ~/access:/var/log/nginx/ nginx-elk
 
3. access log -> redis 에 넣기위해 logstash 로 파이프라인 구성
 
 3-1. logstash.conf
    : 파이프 라인의 input 정보를 설정, path, type, codec, filter / output 구성
      nginx 의 access 로그를 구성하여 redis 에 넘김
 
 3-2. Dockerfile_logstash
    : customizing 한 logstash.conf 파일로 docker image 를 생성
    : build command : docker build -t logstash-elk -f ./Dockerfile_logstash .
    : docker run command : docker run --name logstash-elk ---net=yeoreumab_shin_esnet-link redis-elk:redis-elk -p 9600:9600 -p 5044:5044 -d -v /home/yeoreumab_shin/access:/access logstash-elk
    ( /home/yeoreumab_shin/access 의 log 경로의 경우 2-2 에서 지정한 nginx 의 run command 와 관련이 있다 )
    
4. redis 실행
  : docker run command : docker run --name redis-elk --net=yeoreumab_shin_esnet -d -p 6379:6379 redis

5. redis -> elasticsearch 로 전송하기 위해 logstash 로 파이프라인 구성
 
 5-1. logstash2.conf
    : 파이프 라인의 input 정보(redis) -> output(elasticsearch 의 host)
 
 5-2. Dockerfile_logstatsh2
    : customizing 한 logstash2.conf 파일로 docker image 를 생성
    : build command : docker build -t logstash-redis -f Dockerfile_logstash2 .
    : run command : docker run --name logstash-redis --net=yeoreumab_shin_esnet --link redis-elk:redis-elk --link elasticsearch:elaasticsearch -d logstash-redis

Todo : docker-compose.yml 안에 nginx, logstash, redis, logstash2 동시 구동
       쉘 스크립트 작성
       redis ip 컨테이너 내 공유 방법 찾기
