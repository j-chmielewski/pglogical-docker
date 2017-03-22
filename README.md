# Run on both nodes

```
CREATE ROLE replication WITH SUPERUSER REPLICATION LOGIN ENCRYPTED PASSWORD 'replication';
create database test;
\c test;
create extension pglogical_origin; create extension pglogical;
```
# Run on provider node

```
create table replicated_table (id integer primary key, name varchar); insert into replicated_table values (1, 'test name');
SELECT pglogical.create_node(node_name := 'provider', dsn := 'host=master port=5432 user=replication password=replication dbname=test');
SELECT pglogical.replication_set_add_all_tables('default', ARRAY['public']);
```

# Run on subscriber node

```
create table replicated_table (id integer primary key, name varchar);
SELECT pglogical.create_node(node_name := 'subscriber', dsn := 'host=slave port=5432 user=replication password=replication dbname=test');
SELECT pglogical.create_subscription(subscription_name := 'subscription', provider_dsn := 'host=master  port=5432 user=replication password=replication dbname=test');
```

# Administration

```
select * FROM pglogical.show_subscription_status();
select pglogical.alter_subscription_remove_replication_set('subscription', 'set_from_pglogical.show_subscription_status');
select pglogical.drop_subscription('subscription');
select pglogical.drop_node('provider');
```