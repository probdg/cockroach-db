# cockroach-db

docker network create -d bridge roachnet

docker volume create roach1
docker volume create roach2
docker volume create roach3

docker run -d \
--name=roach1 \
--hostname=roach1 \
--net=roachnet \
-p 26257:26257 -p 8080:8080  \
-v "roach1:/cockroach/cockroach-data"  \
cockroachdb/cockroach:v20.2.8 start \
--insecure \
--join=roach1,roach2,roach3

docker run -d \
--name=roach2 \
--hostname=roach2 \
--net=roachnet \
-v "roach2:/cockroach/cockroach-data" \
cockroachdb/cockroach:v20.2.8 start \
--insecure \
--join=roach1,roach2,roach3

docker run -d \
--name=roach3 \
--hostname=roach3 \
--net=roachnet \
-v "roach3:/cockroach/cockroach-data" \
cockroachdb/cockroach:v20.2.8 start \
--insecure \
--join=roach1,roach2,roach3

docker exec -it roach1 ./cockroach init --insecure
docker exec -it roach1 grep 'node starting' cockroach-data/logs/cockroach.log -A 11
