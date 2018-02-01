# Rethink Migrate
The goal of this docker image is to easily manage RethinkDB migrations through the node [rethink-migrate](https://github.com/JohanObrink/rethink-migrate) tool.

##### VOLUME
- A volume needs to be mounted to this container at `/data/rethinkdb`
- The volume should contain the `database.json` config file and a `migrations` folder will all the migration files. Make sure to specify a proper `host` to be able to connect to the rethinkdb database depending on where that is. For example if rethinkdb is running on your Mac development machine you can pass `docker.for.mac.localhost` as the host in the database.json config file. Alternatively if the database is running through another docker container, you can link the two through docker networks. This is easier if managing all this through docker compose.

##### ENTRYPOINT
- The `rethink-migrate` binary is expose through the ENTRYPOINT
- The previous node command of `rethink-migrate ...` would now become `docker run --rm -v "$PWD":/data/rethinkdb egjiri/rethink-migrate ...`
- This is assuming your volume source is on the current directory otherwise the `$PWD` can be replaced with the absolute path to the volume source data
- Since this is quite a long command you could potentially alias it in .bash_profile back to `rethink-migrate`. To do that, add the following line to your .bash_profile config
```
alias rethink-migrate="docker run --rm -v '$PWD':/data/rethinkdb egjiri/rethink-migrate"
```

##### COMMANDS
- Create a new migration - `create`
```
docker run --rm -v "$PWD":/data/rethinkdb egjiri/rethink-migrate create ...
```

- Run the all migrations - `up`
```
docker run --rm -v "$PWD":/data/rethinkdb egjiri/rethink-migrate up
```

- Rollback last migration - `down`
```
docker run --rm -v "$PWD":/data/rethinkdb egjiri/rethink-migrate down
```

- Rollback all migrations - `down --all`
```
docker run --rm -v "$PWD":/data/rethinkdb egjiri/rethink-migrate down --all
```

---

## Docker Compose
The more likely use-case for this image is through docker-compose.
Here is a sample `docker-compose.yaml` file to get you started.
```yaml
---
version: "3"

services:
  migrate:
    image: egjiri/rethink-migrate
    volumes:
      - .:/data/rethinkdb
    command: "up"
    depends_on:
      - db
  db:
    image: rethinkdb:2.3.6
    ports:
      - 8080:8080
    volumes:
      - rethinkdb_data:/data
```
