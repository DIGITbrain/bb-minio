# General

## Deployment type

docker

## Image

Based on official image on Docker Hub: https://hub.docker.com/r/minio/minio

## Licence

Affero General Public License Version 3 (AGPLv3)

## Version

RELEASE.2023-08-23T10-07-06Z

## Description

MinIO is a high performance, distributed object storage system. It is software-defined, runs on industry standard hardware and is 100% open source with the dominant license being GNU AGPL v3.
MinIO is different in that it was designed from its inception to be the standard in private/hybrid cloud object storage. Because MinIO is purpose-built to serve only objects, a single-layer architecture achieves all of the necessary functionality without compromise. The result is a cloud-native object server that is simultaneously performant, scalable and lightweight. While MinIO excels at traditional object storage use cases like secondary storage, disaster recovery and archiving, it is unique at overcoming the challenges associated with machine learning, analytics and cloud-native application workloads.

# Deployment

A. Native installation:

```sh
curl -O https://downloads.rclone.org/rclone-current-linux-amd64.zip
unzip rclone-current-linux-amd64.zip
cd rclone-*-linux-amd64
sudo cp rclone /usr/bin/
sudo chown root:root /usr/bin/rclone
sudo chmod 755 /usr/bin/rclone
```

B. General example with Docker:

```sh
docker run -d --rm
        --name minio \
        -p 80:9000/tcp \
        -e "MINIO_ACCESS_KEY=username" \
        -e "MINIO_SECRET_KEY=password" \
        -v /host/data:/data \
        minio/minio:RELEASE.2023-08-23T10-07-06Z \
        server /data
```

C. Docker-compose: change the `MINIO_ACCESS_KEY` and `MINIO_SECRET_KEY` environment variables and run:

```
docker-compose up -d
```

## Parameters

|Name|Value|Description|
|-|-|-|
|Ports|`-p 80:9000/tcp` | MinIO port (HTTP) |
|Volumes|`-v $PWD/minio/data:/data`| Persist MinIO data |
<br/>


# Authentication

Use:
```sh
-e MINIO_ACCESS_KEY=username
-e MINIO_SECRET_KEY=password
```

## Testing

Direct your browser at: ```http://<HOST>```, set: user/password.

# TLS

Generate: ```private.key```, ```public.crt```. (fore more details, see: [2]).

```sh
mkdir -p $HOME/.minio/
openssl req -x509 -nodes \
        -days 365 \
        -newkey rsa:2048 \
        -keyout $HOME/.minio/private.key \
        -out $HOME/.minio/public.crt
```

```sh
docker run -d --rm \
        --name minio
        -p 443:443/tcp \
        -e "MINIO_ACCESS_KEY=username" \
        -e "MINIO_SECRET_KEY=password" \
        -v /host/data:/data \
        -v $HOME/.minio/:/root/.minio \
        minio/minio:RELEASE.2021-10-02T16-31-05Z \
        server --certs-dir /root/.minio --address ":443" /data
```

## Testing

Direct your browser at: ```https://<HOST>```, set: user/password.


# References

[1] https://docs.min.io/docs/minio-server-configuration-guide.html

[2] https://docs.minio.io/docs/how-to-secure-access-to-minio-server-with-tls