README
======

This project is inspired by [Jason Wilder](http://jasonwilder.com/blog/2014/07/15/docker-service-discovery/) and [Traefik Project](https://github.com/emilevauge/traefik). 


Model
-----
* When we deploys a docker container, it exposes a named backend service (using `--label="backend=MyBackend"`)
* This container instance monitors such changes using [docker-gen](https://github.com/jwilder/docker-gen) and make corresponding etcd changes

Use Case
--------
* Deployment to the server and Exposing the service should be a separate process, this allows us to do Blue/Green Deployment (or A/B Testing)


Sample Run:
```sh
# docker run -e ETCD_HOST=192.168.0.10:4001 \
	-d --restart=always \
	-e DOCKER_TLS_VERIFY=1 \
	-e DOCKER_HOST=tcp://192.168.0.10:2376 \
	-v /data/dockercert:/.docker \
	--name=etcd_updater starpost/docker-register-etcd
```

Start a new Service:
```sh
# docker run --label="backend=MyBackend" jwilder/whoami
```

Etcd Tree Result:
```sh
# etcdctl -C 192.168.0.10:4001 ls --recursive /backends
/backends
/backends/MyBackend
/backends/MyBackend/1d4533cf99b3
/backends/MyBackend/1d4533cf99b3/url

# etcdctl -C 192.168.0.10:4001 get /backends/MyBackend/1d4533cf99b3/url
192.168.56.17:8080
```

