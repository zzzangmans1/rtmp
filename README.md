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
