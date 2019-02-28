# PostgreSQL HA Setup in minutes

## Let's use stolon to start PostgreSQL server

> Prerequisite: [Consul](https://www.consul.io/) server should be running in local

### starting pg - stolon keeper

```bash
# node0

stolon-keeper --cluster-name stolon-cluster \
--store-backend=consul \
--uid postgres0 \
--data-dir data/postgres0 \
--pg-su-password=supassword \
--pg-repl-username=repluser \
--pg-repl-password=replpassword \
--pg-listen-address=127.0.0.1 \
--pg-port 5435

```

```bash
# node1

stolon-keeper --cluster-name stolon-cluster \
--store-backend=consul \
--uid postgres1 \
--data-dir data/postgres1 \
--pg-su-password=supassword \
--pg-repl-username=repluser \
--pg-repl-password=replpassword \
--pg-listen-address=127.0.0.1 \
--pg-port 5436
```

### starting pg - stolon sentinel

```bash
# node2

stolon-sentinel --cluster-name stolon-cluster \
--store-backend=consul

```

### starting pg - stolon proxy

```bash
# node3

stolon-proxy --cluster-name stolon-cluster \
--store-backend=consul

```

### Let's initialise

```bash
# node4

stolonctl --cluster-name stolon-cluster \
--store-backend=consul \
init --yes

```

### Let's create sample data.

```bash
PGPASSWORD=supassword psql -h 127.0.0.1 -U ${whomai} -d postgres
```

```sql
CREATE DATABASE pgconf;

CREATE TABLE attendees(id int primary key, name varchar);

INSERT INTO attendees values (1, 'Krishnaswamy');

SELECT * FROM attendees
```

### For more info

https://github.com/sorintlab/stolon

> No overhead in running PostgreSQL, basically it manages starting / stopping PostgreSQL and configuring it using consul/etcd as config backend
