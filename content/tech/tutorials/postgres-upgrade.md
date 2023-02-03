# upgrading postgresql on arch (in case you fucked up)

```sh
stop postgresql
stop akkoma   # make sure nothing is trying to hit the database
```

## pg_upgrade will fail if you initdb with different params

so i'd made a file-level backup at least

```sh
sudo mv /var/lib/postgres/data /var/lib/postgres/olddata
```

i tried to 

```
sudo mkdir /var/lib/postgres/data /var/lib/postgres/tmp
sudo chown postgres:postgres /var/lib/postgres/data /var/lib/postgres/tmp

sudo -iu postgres
cd /var/lib/postgres/tmp
initdb -D /var/lib/postgres/data --locale=en_US.UTF-8 --encoding=UTF8 --data-checksums
```

this caused pg_upgrade to fail because the old data didn't have `--data-checksums` enabled

## pg_dumpall will fail with extensions like rum

ok so that didn't work, let's try the manual dump and reload. exit out of the postgres session and back to your user

```sh
yay -S postgresql-old-upgrade
sudo rm -r /var/lib/postgres/data
sudo mkdir /var/lib/postgres/data
sudo chown -R postgres:postgres /var/lib/postgres/data

sudo -iu postgres
initdb -D /var/lib/postgres/data
/opt/pgsql-14/bin/pg_ctl -D /var/lib/postgres/olddata/ start
```

the old version should be running now

```sh
pg_dumpall -h /tmp -f /tmp/old_backup.sql
```

aaaaand this failed because of something to do with pg_rum

## downgrading to get a good dump

ok let's kill the old postgres

```sh
sudo killall postgres
```

copy the old data over and set permissions

```sh
sudo rm -r /var/lib/postgres/data
sudo cp -r /var/lib/postgres/olddata /var/lib/postgres/data
sudo chown -R postgres:postgres /var/lib/postgres/data
```

downgrade to the last version of postgres

```sh
yay -S downgrade
sudo downgrade postgresql
start postgresql
```

dump

```sh
pg_dumpall > /tmp/dumpall.sql
```

now we can upgrade again?

```sh
stop postgresql
yay -S postgresql
yay -S pg_rum     # do a clean build?
```

now we try to restore data

```
sudo rm -r /var/lib/postgres/data
sudo mkdir /var/lib/postgres/data
sudo chown -R postgres:postgres /var/lib/postgres/data

sudo -Hu postgres initdb -D /var/lib/postgres/data --locale=en_US.UTF-8 --encoding=UTF8 --data-checksums

start postgresql

sudo -Hu postgres psql -f /tmp/dumpall.sql template1
```

this worked for me

## etc

while you're here you might as well update and migrate akkoma if you're still running that?

```sh
sudo -Hu akkoma MIX_ENV=prod mix deps.get
sudo -Hu akkoma MIX_ENV=prod mix ecto.migrate
start akkoma
```