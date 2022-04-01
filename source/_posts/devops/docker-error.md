title: docker-compose build报错
date: 2021-08-17 15:20:08
tags: docker
categories: docker
---

```
% docker-compose build
Building chrome
[+] Building 0.4s (3/3) FINISHED                                                                                                                                                                                                                                              
=> [internal] load build definition from Dockerfile2                                                                                                                                                                                                                    0.0s
=> => transferring dockerfile: 38B                                                                                                                                                                                                                                      0.0s
=> [internal] load .dockerignore                                                                                                                                                                                                                                        0.0s
=> => transferring context: 34B                                                                                                                                                                                                                                         0.0s
=> ERROR [internal] load metadata for docker.io/library/ubuntu:18.04                                                                                                                                                                                                    0.3s
------
> [internal] load metadata for docker.io/library/ubuntu:18.04:
------
failed to solve with frontend dockerfile.v0: failed to create LLB definition: failed to do request: Head https://hub-mirror.c.163.com/v2/library/ubuntu/manifests/18.04?ns=docker.io: Bad Request
ERROR: Service 'chrome' failed to build : Build failed
```

执行

```
export DOCKER_BUILDKIT=0
export COMPOSE_DOCKER_CLI_BUILD=0
```
