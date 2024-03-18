# 우분투 Nginx 설치
### Nginx 패키지를 설치
``` commend
apt install nginx
```

### Nginx 서비스의 상태를 확인
``` commend
service nginx status
```

### Nginx 설정 파일 확인
```
vi /etc/nginx/sites-available/default
```

### Nginx - Tomcat 설정
```
http {
    server {
    listen       80;
    server_name  localhost;

    client_max_body_size 1000M;
   	location / {
            proxy_pass http://localhost:8080;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
        }
    }
}
```