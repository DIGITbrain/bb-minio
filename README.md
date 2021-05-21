# bb-minio
Building block for MinIO


# IMAGE SOURCE
Official image on __Git Hub__: https://github.com/minio/minio

# AUTHENTICATION

> docker run -d -v /host/data:/data -p 80:9000/tcp --name minio __-e "MINIO_ACCESS_KEY=username" -e "MINIO_SECRET_KEY=password"__  minio/minio server /data

# HTTPS

Generate: private.key, public.crt -> $HOME/.minio/certs (see: https://docs.minio.io/docs/how-to-secure-access-to-minio-server-with-tls).

> mkdir -p $HOME/.minio/
>
> openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout $HOME/.minio/private.key -out $HOME/.minio/public.crt
>
> docker run -d -v /host/data:/data __-v $HOME/.minio/:/root/.minio__ -p 443:443/tcp --name minio1 -e "MINIO_ACCESS_KEY=username" -e "MINIO_SECRET_KEY=password"  minio/minio server __--certs-dir /root/.minio__ --address ":443" /data


## Rclone integration

### RCLONE INSTALL
>  curl -O https://downloads.rclone.org/rclone-current-linux-amd64.zip
>
>  unzip rclone-current-linux-amd64.zip
>
>  cd rclone-*-linux-amd64
>
>  sudo cp rclone /usr/bin/
>
>  sudo chown root:root /usr/bin/rclone
>
>  sudo chmod 755 /usr/bin/rclone


### RCLONE CONFIG
>  ~/.config/rclone$ cat /home/ubuntu/.config/rclone/rclone.conf
>
> [s3-test]
>
> type = s3
>
> provider = http://193.224.59.150:80
>
> access_key_id = username
>
> secret_access_key = password
>
> region = us-east-1
>
> endpoint = http://xxx.xxx.xxx.xxx:80

### RCLONE TEST
> rclone lsd s3-test:/
>
> rclone ls s3-test:bucket

### RCONE MOUNT/UNMOUNT
>  rclone mount -vv --daemon --dir-cache-time 5s s3-test:akos ./mount
>
>  fusermount -uz /./rclone-s3-mount/

