# docker-postgresql-cli

Minimal docker image based on Alpine with PostgreSQL client libraries on top.

To use:

```
# Check that postgres is running on some.host.example.com
$ docker run --rm stephenc/postgresql-cli pg_isready --host some.host.example.com --port 5432 --dbname somedb --user someuser
some.host.example.com:5432 - no response

# Assuming you have the connection details in your environment already
$ docker run --rm -e PGHOST -e PGDATABASE -e PGPORT -e PGUSERNAME -e PGPASSWORD stephenc/postgresql-cli psql
...
```

Can also be used as an init container for Kubernetes to force the pod to wait until PostgreSQL is ready, e.g.

```
      initContainers:
        - name: check-pg-ready
          image: stephenc/postgresql-cli:latest
          command:
            - "sh"
            - "-c"
            - "rv=0; for n in $(seq 1 90) ; do pg_isready && rv=0 && break || rv=$? && echo 'Waiting for PostgreSQL'; sleep 1; done; exit $rv"
          env:
            - name: "PGHOST"
              value: ...
            - name: "PGPORT"
              value: ...
            - name: "PGUSER"
              value: ...
            # The password is strictly not required for running pg_isready, illustrating in case you need
            # an init container to run scripts against the database 
            - name: "PGPASSWORD"
              valueFrom:
                secretKeyRef:
                  name: ...
                  key: ...
            - name: "PGDATABASE"
              value: ...

```
