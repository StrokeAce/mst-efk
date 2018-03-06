## Build Docker images for fluentd

```
docker build -t demo/fluentd:v0.14 .
```


**NOTE**
Use domain name `docker.for.mac.localhost` as Consul backend address.
See more details in [CONNECT FROM A CONTAINER TO A SERVICE ON THE HOST](https://docs.docker.com/docker-for-mac/networking/#per-container-ip-addressing-is-not-possible)

## Launch Consul agent

```
consul agent -dev -node=consul -data-dir=/tmp/consul
```

Then open [http://127.0.0.1:8500/](http://127.0.0.1:8500/) and create `Key/Value`:

* `fluentd/source`

```
<source>
  @type forward
  port 5140
</source>
<source>
   @type beats
   metadata_as_tag
   <parse>
      @type json
   </parse>
</source>
```

* `fluentd/filter`

```
<filter nginx.**>
  @type parser
  format json
  key_name log
  reserve_data true
</filter>
```

* `fluentd/match`

```
<match nginx.backend>
   @type elasticsearch
   host elasticsearch
   port 9200
   logstash_format true
   logstash_prefix nginx
</match>
<match docker.**>
   @type elasticsearch
   host elasticsearch
   port 9200
   logstash_format true
   logstash_prefix app
</match>
```

## Start services

```
docker-compose up
```
