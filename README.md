# Nginx rtmp with more features

Tested nginx version: 1.15.8    

## Added features

* Http/flv output support

## Config

### http/flv output

The code implements this feature is contained in `ngx_rtmp_http_live_module.h` and
`ngx_rtmp_http_live_module.c`.     
The original nginx rtmp code is not changed except a small modification for `ngx_rtmp_codec_module`: add a field `raw_meta`. We need it to build flv metadata.

The example config for using these module is like this
```
rtmp {
    server {
        listen 19350;

        http_live_src mylive;

        application myapp {
            live on; 
            http_live on; 
        }   
    }   
}

http {
    server {
        listen       8080;

        location /myapp {
            http_rtmp_live_dst mylive;
            http_rtmp_live on;
        }
    }
}
```

`http_rtmp_live_dst` and `http_live_src` are paired, for example:    
When we visit a http/flv stream `http://127.0.0.1:8080/myapp/test.flv`, it will find rtmp server block contains the `http_live_src` as `mylive`, and find app `myapp` and stream name `test`.
So if we using ffmpeg to push rtmp stream to `rtmp://127.0.0.1:19350/myapp/test`,  the stream data will send to the http/flv client.

### DEV Notes

http-live-flv notes(https://dista.work/dev/2019/02/17/ngx-rtmp-http-live.html)
