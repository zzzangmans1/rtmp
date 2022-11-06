# rtmp
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

