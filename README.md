# HANA Docker (Compose) Template

- I need HANA to develop
- I have a dump (`EXPORT ... AS CSV INTO ...`) that I want to import
- I want a turn-key solution

## Prerequisites

`docker pull` the latest [HANA Express Edition image][image] from the Docker Store

[image]: https://hub.docker.com/_/sap-hana-express-edition/

> **Docker for Mac / Docker for Windows**\
> Make sure to increase the RAM (at least **12GB**), the CPU (4 cores) and, depending on your dump, the disk size of the Docker VM!

## Usage Instructions

1. Download this repository as zip
1. Unpack
1. Rename the folder to something more descriptive
1. Dump the `post_start` hook scripts of the HANA image into this folder
    ```bash
    docker create --name hana_dummy store/saplabs/hanaexpress:<version>
    docker cp hana_dummy:/hana/hooks/post_start .
    docker rm hana_dummy
    ```
1. Adapt the HANA image version in `docker-compose.yml`
1. Upack the dump into a sub-folder of `./dump`, e.g. `./dump/SOURCE_SCHEMA`
1. Adapt the env vars in `docker-compose.yml` as necessary (check `post_start/999_import_dump` to understand how they are
   used during bootstrap)
1. Tweak `post_start/999_import_dump` as necessary
1. *(optional)* Add some more shell scripts to `post_start` if you need other things executed during the database bootstrap
1. `docker-compose up`
1. Grab a pint of :coffee: and wait until the database setup including dump import is done.

## Notes

- This setup modifies the tenant database
- The `SYSTEM` password for both the system and the tenant (HXE) database is `HXEHana1`

```
db.url=jdbc:sap://localhost:39041/?databaseName=HXE&reconnect=true&statementCacheSize=512
db.driver=com.sap.db.jdbc.Driver
db.username=LOCALDEV
db.password=Localdev1
hanadb.storage.columnbased=true
```
