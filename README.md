# Media Solution Server

## Nginx install

// NGINX index.html 주소
cd /usr/share/nginx/html

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

        application live {
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

## systemctl 에 Nginx 등록

systemd 폴더에 새 파일을 만듭니다.
```
vi /lib/systemd/system/nginx.service
```

이 구성 파일을 복사하여 파일에 붙여넣습니다.

```
[Unit]
Description=Nginx Custom From Source
After=syslog.target network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t
ExecStart=/usr/sbin/nginx
ExecReload=/usr/sbin/nginx -s reload
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

시스템을 다시 로드
```
systemctl daemon-reload
```

서버가 부팅될 때 자동으로 시작되도록 Nginx 서비스를 활성화합니다.

```
systemctl enable nginx
```

service를 사용하여 Nginx 제어

```
service nginx start
service nginx reload
service nginx stop
service nginx restart
```

nginx 시작

```
service nginx start
```

OBS에서 스트리밍 주소 세팅합니다.

![image](https://user-images.githubusercontent.com/52357235/200564494-f640c991-d4a8-4eef-9941-c8c512bcd3a8.png)

![image](https://user-images.githubusercontent.com/52357235/200565056-19ad826e-9b5f-4055-8e66-1f81b266f1ce.png)
그리고 VLC 에서 주소를 불러와 재생합니다.

<img width="416" alt="image" src="https://user-images.githubusercontent.com/52357235/200564698-04cd99ac-f2a8-4124-8085-e9a7c26f262a.png">

<img width="668" alt="image" src="https://user-images.githubusercontent.com/52357235/200565018-aa2d3b87-641a-4322-ae4c-d652386e3a18.png">


## hls 로 변환하여 VLC로 실행

/etc/nginx/nginx.conf 에서 
rtmp 부분

```
application live {
                        live on;
                        record off;
               		
			#HLS
			hls on;
                        hls_path /etc/nginx/live;
                        hls_fragment 3;
                        hls_playlist_length 60;

                        dash on;
                        dash_path /var/www/html/stream/dash;
		 }
```

그리고 http 부분

```
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
```

<img width="520" alt="image" src="https://user-images.githubusercontent.com/52357235/200567775-fc77081d-345b-421a-97b7-d0a96aa99d4a.png">

<img width="1277" alt="image" src="https://user-images.githubusercontent.com/52357235/200567245-3b35b271-cd64-430c-b8f3-94e35d90d21b.png">


## (OBS) rtmp -> (WEB)HLS : (ANDROID)HLS [PLAYER_ANDROID]

웹사이트에서 HLS 재생코드
```
<!DOCTYPE html>
<html>
<head>
<meta charset=utf-8 />
<title>hls.js</title>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
</head>
<body>
<video controls autoplay id="video-player"></video>
<script>
	var video = document.getElementById('video-player');
	var videoSrc = 'http://{m3u8의 주소}.m3u8';
	//
	// First check for native browser HLS support
	//
	if (video.canPlayType('application/vnd.apple.mpegurl')) {
		video.src = videoSrc;
		//
		// If no native HLS support, check if hls.js is supported
		//
	} else if (Hls.isSupported()) {
		var hls = new Hls();
		hls.loadSource(videoSrc);
		hls.attachMedia(video);
	}
</script>
</body>
</html>
```

![image](https://user-images.githubusercontent.com/52357235/200588545-8324f99d-b382-47fb-b2b9-1368b174a41a.png)

<img width="464" alt="image" src="https://user-images.githubusercontent.com/52357235/200588631-d042d340-8848-4835-a50a-a8198d1c21cf.png">
