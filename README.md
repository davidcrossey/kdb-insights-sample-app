# kxi-microservices-data-services
- [kxi-microservices-data-services](#kxi-microservices-data-services)
  - [Quick Links](#quick-links)
    - [Storage Manager](#storage-manager)
    - [Data Access](#data-access)
    - [Service Gateway](#service-gateway)
  - [Authentication](#authentication)
  - [Create data folders](#create-data-folders)
  - [Docker start](#docker-start)
  - [Query Data](#query-data)
  - [Custom API](#custom-api)

## Quick Links

<https://code.kx.com/insights/microservices/intro.html>

### Storage Manager

* <https://code.kx.com/insights/microservices/storage-manager/introduction.html>
* <https://code.kx.com/insights/microservices/artefacts.html#storage-manager>

### Data Access

* <https://code.kx.com/insights/microservices/data-access/introduction.html>
* <https://code.kx.com/insights/microservices/artefacts.html#data-access>

### Service Gateway

* <https://code.kx.com/insights/microservices/data-access/introduction_sg.html>
* <https://code.kx.com/insights/microservices/artefacts.html#service-gateway>

## Authentication
```bash
## create file and populate appropriately
$ tee .cloud_auth_env << EOF
export AWS_REGION=
export AWS_ACCESS_KEY_ID=
export AWS_SECRET_ACCESS_KEY=
export AZURE_STORAGE_ACCOUNT=
export AZURE_STORAGE_SHARED_KEY=
export GOOGLE_TOKEN=
export GCLOUD_PROJECT_ID=
EOF
```

## Create data folders 
```bash
$ cd kxi-microservices-data-services
$ mkdir -p data/db/hdb/data tplog cache
$ aws s3 cp s3://microservices-data-db/sym data/db/hdb/data
$ sudo chmod 777 -R data tplog cache
```

## Docker start
```bash
$ docker login registry.dl.kx.com
$ docker-compose up -d
$ docker-compose logs -f 
```


## Query Data
```q
q)gw:hopen "J"$last ":" vs first system"docker port kxi-microservices-data-services-sggw-1"
// q)gw(`.kxi.getData;(`table`startTS`endTS)!(`quote;"p"$.z.d-1;"p"$.z.d+1);`f;(0#`)!())
q)gw(`.kxi.getData;(`table`startTS`endTS)!(`trade;"p"$.z.d;.z.p);`f;(0#`)!())
```
```bash
curl -X POST --header "Content-Type: application/json"\
 --header "Accepted: application/json"    \
 --data '{ "table":  "quote", "startTS":"2022.02.10D00:00:00.000", "endTS":"2023.02.12D00:00:00.000"}'\
  `docker port kxi-microservices-data-services-sggw-1 | grep 8080 | cut -f3 -d " "`"/kxi/getData"
```

## Custom API
```q
q)gw(`.custom.countBy;(`table`startTS`endTS`byCols)!(`quote;"p"$.z.d-1;"p"$.z.d+1;`bidPrice);`f;(0#`)!())
```