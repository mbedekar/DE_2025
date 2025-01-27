#################
Q1
#################

(base) PS C:\Users\manda\Documents\data-engineering-zoomcamp\Homeworks\HW1> docker build -t hw1:q1 .
[+] Building 6.3s (6/6) FINISHED                                                                          docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                      0.1s
 => => transferring dockerfile: 80B                                                                                       0.0s
 => [internal] load metadata for docker.io/library/python:3.12.8                                                          1.5s
 => [auth] library/python:pull token for registry-1.docker.io                                                             0.0s
 => [internal] load .dockerignore                                                                                         0.0s
 => => transferring context: 2B                                                                                           0.0s
 => [1/1] FROM docker.io/library/python:3.12.8@sha256:2e726959b8df5cd9fd95a4cbd6dcd23d8a89e750e9c2c5dc077ba56365c6a925    2.1s
 => => resolve docker.io/library/python:3.12.8@sha256:2e726959b8df5cd9fd95a4cbd6dcd23d8a89e750e9c2c5dc077ba56365c6a925    0.1s
 => => sha256:12f3828c4288e05db725a33e56a3f2841e70fb6865584eb9993a9bde160f0f44 6.16MB / 6.16MB                            0.7s
 => => sha256:4d8be491b8661974ed63e9e6380858934cdd98c97eb664df0fc69690444d8e63 25.65MB / 25.65MB                          1.7s
 => => sha256:ec162e081748166453ab187053569aac7502676d3d4571bd9fc021b215fdb038 250B / 250B                                0.5s
 => exporting to image                                                                                                    3.8s
 => => exporting layers                                                                                                   0.0s
 => => exporting manifest sha256:685cd98e38febdbdcd321535ea50e6bfb0d88876425cf5bba1881cbb598cf375                         0.0s
 => => exporting config sha256:156562654ac76ab81b5adb65d8aef47427609e71a90107aac11dbe4aa7faf3a4                           0.1s
 => => exporting attestation manifest sha256:01c4432f8a26c16d16bc639e0e542b33bd89207a1d4c562eb00aa086aed4168c             0.1s
 => => exporting manifest list sha256:a189ebbd97d9d1453ea6d0c847b212de51b073795ffefdaf9e79ce928d3188a2                    0.0s
 => => naming to docker.io/library/hw1:q1                                                                                 0.0s
 => => unpacking to docker.io/library/hw1:q1                                                                              3.5s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/wj9rh245bux3y4k23uz9bcz61
(base) PS C:\Users\manda\Documents\data-engineering-zoomcamp\Homeworks\HW1> docker run -it hw1:q1
root@ef4f34b2cecf:/# pip --version
pip 24.3.1 from /usr/local/lib/python3.12/site-packages/pip (python 3.12)
root@ef4f34b2cecf:/#

########################
Q2
########################




#######################
Q3-Q6
#######################

-----------------------------------------------------------------
Q3:
-----------------------------------------------------------------

select count(1) from green_taxi_data
where (date(lpep_pickup_datetime) between '2019-10-01' and '2019-11-01'
and date(lpep_dropoff_datetime) between '2019-10-01' and '2019-11-01')
and trip_distance <= 1;

select count(1) from green_taxi_data
where (date(lpep_pickup_datetime) between '2019-10-01' and '2019-11-01'
and date(lpep_dropoff_datetime) between '2019-10-01' and '2019-11-01')
and trip_distance > 1
and trip_distance <= 3;

select count(1) from green_taxi_data
where (date(lpep_pickup_datetime) between '2019-10-01' and '2019-11-01'
and date(lpep_dropoff_datetime) between '2019-10-01' and '2019-11-01')
and trip_distance > 3
and trip_distance <= 7;

select count(1) from green_taxi_data
where (date(lpep_pickup_datetime) between '2019-10-01' and '2019-11-01'
and date(lpep_dropoff_datetime) between '2019-10-01' and '2019-11-01')
and trip_distance > 7
and trip_distance <= 10;

select count(1) from green_taxi_data
where (date(lpep_pickup_datetime) between '2019-10-01' and '2019-11-01'
and date(lpep_dropoff_datetime) between '2019-10-01' and '2019-11-01')
and trip_distance > 10;
---104,838; 199,013; 109,645; 27,688; 35,202


--------------------------------------------------------------------
Q4:
--------------------------------------------------------------------

select date(lpep_pickup_datetime)
from green_taxi_data
where trip_distance in (select max(trip_distance)
			from green_taxi_data);
---2019-10-31


--------------------------------------------------------------------
Q5:
--------------------------------------------------------------------

select * from
(select tzl."Zone", sum(total_amount) Zone_Total
from green_taxi_data gta, taxi_zone_lookup tzl
where date(gta.lpep_pickup_datetime) = '2019-10-18'
and gta."PULocationID" = tzl."LocationID"
group by tzl."Zone")
where Zone_Total >= 13000
order by Zone_Total desc;
---East Harlem North, East Harlem South, Morningside Heights

--------------------------------------------------------------------
Q6:
--------------------------------------------------------------------

select tzl1."Zone"
from green_taxi_data gtd, taxi_zone_lookup tzl, taxi_zone_lookup tzl1
where date(lpep_pickup_datetime) between '2019-10-01' and '2019-10-31'
and tzl."Zone" = 'East Harlem North'
and tzl."LocationID" = gtd."PULocationID"
and gtd.tip_amount = (select max(gtd.tip_amount)
					from green_taxi_data gtd, taxi_zone_lookup tzl
					where date(lpep_pickup_datetime) between '2019-10-01' and '2019-10-31'
					and tzl."Zone" = 'East Harlem North'
					and tzl."LocationID" = gtd."PULocationID")
and gtd."DOLocationID"=tzl1."LocationID"
---JFK Airport