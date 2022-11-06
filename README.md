# Media Solution Server

## Nginx install

[참고 사이트](https://facsiaginsa.com/nginx/adaptive-bitrate-streaming-server-nginx-ubuntu) </br>

Nginx 종속성 설치

```
apt update && apt install build-essential git libpcre3-dev libssl-dev zlib1g-dev
```

Nginx 소스 코드 다운로드 후 압축풀기

```
wget http://nginx.org/download/nginx-1.20.2.tar.gz
tar -zxvf nginx-1.20.2.tar.gz
```

RTMP 모듈 다운로드

```
wget https://codeload.github.com/arut/nginx-rtmp-module/tar.gz/refs/tags/v1.2.2
tar -zxvf v1.2.2
```

Nginx 빌드 및 설치

```
cd nginx-1.20.2
```

module 빌드
```
./configure \ 
    --prefix=/etc/nginx \ 
    --conf-path=/etc/nginx/nginx.conf \ 
    --error-log-path=/var/log/nginx/error.log \ 
    --http -log-path=/var/log/nginx/access.log \ 
    --pid-path=/run/nginx.pid \ 
    --sbin-path=/usr/sbin/nginx \ 
    --with-http_ssl_module \ 
    -- with-http_v2_module \ 
    --with-http_stub_status_module \ 
    --with-http_realip_module \ 
    --with-file-aio \ 
    --with-threads \ 
    --with-stream \
    --add-module=../nginx-rtmp-module-1.2.2
```

```
make && make install

nginx -v
```

## 스트리밍 서버 구성

```
cd /etc/nginx
```

기본 Nginx 구성 파일 백업
```
mv nginx.conf nginx.conf.old
```
새 구성파일 만들기
```
nano nginx.conf
```

이 구성을 구성 파일에 추가
```
user www-data;
worker_processes auto;
worker_rlimit_nofile 8192;
pid /run/nginx.pid;
rtmp_auto_push on;

events {
    worker_connections 4096;
}

rtmp {
    server {
        listen 1935;
        chunk_size 4096;
        max_message 1M;

        application streaming {
            live on;
            hls on;
            hls_nested on;
            hls_path /etc/nginx/live;

            hls_fragment 6s;
            hls_playlist_length 30s;
        }
    }
}

http {
    server {
        listen 8080;
        root /etc/nginx;

        location /live {
            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }
            add_header Cache-Control no-cache;
        }
    }
}

```
구성 완료후 
```
nginx -t
```
## rtmp
rtmp kali linux server build

**sudo apt install nginx**

**sudo apt install libnginx-mod-rtmp**

**sudo nano /etc/nginx/nginx.conf**

insert text below 

```
rtmp {
  server {
    listen 1935;
    chunk_size 4096;
    
    application live {
      live on;
      record off;
    }
  }
}
```

**sudo systemctl restart nginx**

Check if the service is open with systemctl

<img width="318" alt="image" src="https://user-images.githubusercontent.com/52357235/200170022-5d91d517-7896-48a9-bee8-812026dc2faa.png">

Check the nginx server

127.0.0.1/index.nginx-debian.html access to homepage

<img width="918" alt="image" src="https://user-images.githubusercontent.com/52357235/200170671-949d3161-2890-4b4a-bead-922b09566b72.png">



``` html
<!DOCTYPE html>
<html>
<head>
<meta charset=utf-8 />
  <link href="https://unpkg.com/video.js/dist/video-js.css" rel="stylesheet">
</head>
<body>
  <video id="my_video_1" class="video-js vjs-default-skin" controls preload="auto" width="640" height="360"
  data-setup='{}'>
    <source src="rtmp://192.168.0.13/live/stream" type="rtmp/flv">
  </video>

  <video id="my_video_2" class="video-js vjs-default-skin" controls preload="auto" width="640" height="360"
  data-setup='{}'>
    <source src="http://192.168.0.13:8080/hls/stream.m3u8" type="application/x-mpegURL">
  </video>

  <script src="https://unpkg.com/video.js/dist/video.js"></script>
  <script src="https://unpkg.com/videojs-flash/dist/videojs-flash.js"></script>
  <script src="https://unpkg.com/videojs-contrib-hls/dist/videojs-contrib-hls.js"></script>

</body>
</html>
```

이제 웹사이트에서 flash 를 지원하지 않아 rmtp로 받아도 재생시킬수가 없다......
그래서 hls 도전중
