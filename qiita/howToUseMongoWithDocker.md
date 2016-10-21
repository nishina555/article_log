## Preparation
### 1. Preparation on local environment

Pull docker image
```
$ docker run --name some-mongo -d mongo

Unable to find image 'mongo:latest' locally
latest: Pulling from library/mongo

6a5a5368e0c2: Pull complete
068577f76f42: Pull complete
40a9ca5f1cfb: Pull complete
```

Check the pulled image
```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
mongo                         latest              3dfc45693d00        2 weeks ago         342.5 MB
```

Check the process
```
$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
6d9206300ab1        mongo               "/entrypoint.sh mongo"   6 minutes ago       Up 6 minutes        27017/tcp           some-mongo
```

Copy test data from local to docker os
```
$ docker cp import.json some-mongo:import.json
```

Login docker os
```
$ docker exec -it some-mongo bash
```

### 2. Preparation on docker OS

Import test data which copied from local
```
# mongoimport --db test --collection testcollection --type json --file import.json
```

Execute mongo
```
# mongo

```

## How to rollback
If you want to delete docker process
```
docker rm -f some-mongo
```

If you want to delete mongo image
```
docker rmi mongo
```
