# Media Solution Server

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
