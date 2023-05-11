# sqlvsnosql
Description : Files for workshop SQL vs NoSQL workshop

# Prérequis :
- Ubuntu 20.04+ (VirtualBox, Hyper-V, VMPlayer, AWS / Azure / GCP, etc...)
- Docker 20.10+
- Une connexion internet
- Accès github : https://github.com/Capdata/sqlvsnosql

# 1) Setup MySQL 8 container image 
ref : https://hub.docker.com/_/mysql

<pre><code>$ docker run -tid --name mysqldevfest -e MYSQL_ROOT_PASSWORD=blablabla mysql:8.0
59ceeb312cc475b57fa57f16ab9d804004bd1324d0084117ad4591b77685947c

$ docker ps 
CONTAINER ID   IMAGE        COMMAND                  CREATED              STATUS              PORTS                                       NAMES
59ceeb312cc4   mysql:8.0    "docker-entrypoint.s…"   About a minute ago   Up About a minute   3306/tcp, 33060/tcp                         mysqldevfest
4f46e0c87b04   registry:2   "/entrypoint.sh /etc…"   16 months ago        Up 16 minutes       0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   registry

$ docker exec -it mysqldevfest bash
bash-4.4# mysql --user=root --password=blablabla --execute="select version();"
mysql: [Warning] Using a password on the command line interface can be insecure.
+-----------+
| version() |
+-----------+
| 8.0.33    |
+-----------+
</pre></code>

# 2) Setup MongoDB container image 
ref : https://hub.docker.com/_/mongo
<pre><code>$ docker run -tid --name mongodevfest mongo:4.4-focal
8b3202f55036bbb31f4307741f183d429f79d2956865f40df76974621d7ec479


  $ docker ps
  CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS                                       NAMES
  8b3202f55036   mongo:4.4-focal   "docker-entrypoint.s…"   10 seconds ago   Up 10 seconds   27017/tcp                                   mongodevfest
  59ceeb312cc4   mysql:8.0         "docker-entrypoint.s…"   7 minutes ago    Up 7 minutes    3306/tcp, 33060/tcp                         mysqldevfest
  4f46e0c87b04   registry:2        "/entrypoint.sh /etc…"   16 months ago    Up 22 minutes   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   registry

$ docker exec -it mongodevfest bash
root@8b3202f55036:/# mongo --quiet --eval "db.version()"
4.4.21
</pre></code>

# 3) Initialize dataset 
ref : http://insideairbnb.com/get-the-data/

<pre><code>$ docker exec -it mongodevfest bash
root@8b3202f55036:/# apt-get update
root@8b3202f55036:/# apt-get install wget
root@8b3202f55036:/# wget http://data.insideairbnb.com/france/ile-de-france/paris/2023-03-13/data/listings.csv.gz
root@8b3202f55036:/# wget http://data.insideairbnb.com/france/ile-de-france/paris/2023-03-13/data/reviews.csv.gz
root@8b3202f55036:/# gzip -d listings.csv.gz reviews.csv.gz 

root@8b3202f55036:/# mongoimport --type=csv --headerline --drop --db sample_airbnb --collection listings --file listings.csv
2023-05-11T12:59:24.854+0000	connected to: mongodb://localhost/
2023-05-11T12:59:24.855+0000	dropping: sample_airbnb.listings
2023-05-11T12:59:27.855+0000	[#############...........] sample_airbnb.listings	74.3MB/128MB (58.2%)
2023-05-11T12:59:30.005+0000	[########################] sample_airbnb.listings	128MB/128MB (100.0%)
2023-05-11T12:59:30.005+0000	56726 document(s) imported successfully. 0 document(s) failed to import.

root@8b3202f55036:/# mongoimport --type=csv --headerline --drop --db sample_airbnb --collection reviews --file reviews.csv
2023-05-11T12:59:49.143+0000	connected to: mongodb://localhost/
2023-05-11T12:59:49.143+0000	dropping: sample_airbnb.reviews
2023-05-11T12:59:52.145+0000	[###.....................] sample_airbnb.reviews	69.3MB/427MB (16.2%)
2023-05-11T12:59:55.143+0000	[#######.................] sample_airbnb.reviews	134MB/427MB (31.3%)
2023-05-11T12:59:58.143+0000	[##########..............] sample_airbnb.reviews	191MB/427MB (44.8%)
2023-05-11T13:00:01.143+0000	[##############..........] sample_airbnb.reviews	250MB/427MB (58.5%)
2023-05-11T13:00:04.143+0000	[################........] sample_airbnb.reviews	298MB/427MB (69.8%)
2023-05-11T13:00:07.143+0000	[##################......] sample_airbnb.reviews	338MB/427MB (79.1%)
2023-05-11T13:00:10.143+0000	[#####################...] sample_airbnb.reviews	378MB/427MB (88.4%)
2023-05-11T13:00:13.143+0000	[#######################.] sample_airbnb.reviews	423MB/427MB (99.1%)
2023-05-11T13:00:13.384+0000	[########################] sample_airbnb.reviews	427MB/427MB (100.0%)
2023-05-11T13:00:13.384+0000	1406845 document(s) imported successfully. 0 document(s) failed to import.

root@8b3202f55036:/# mongo sample_airbnb --quiet --eval="db.listings.count()"
56726
root@8b3202f55036:/# mongo sample_airbnb --quiet --eval="db.reviews.count()"
1406845
</pre></code>

