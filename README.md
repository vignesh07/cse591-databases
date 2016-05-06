# CSE591- Advance topics in databases
Repository for the advances in databases class project

The dataset is initialized in the PostgreSQL database along with a POSTGIS extender for evaluating spatial queries. The following queries were executed on the NYC Census Dataset from the New York City Government.

--Index Creation

--drop index nyc_subway_stations_geom_gist;

--drop index nyc_neighborhoods_geom_gist;

--create index nyc_subway_stations_geom_gist on public.nyc_subway_stations using gist((geom));

--create index nyc_neighborhoods_geom_gist on public.nyc_subway_stations using btree(st_x(geom));

--create index nyc_subway_stations_geom_gist on public.nyc_subway_stations using btree(st_x(geom));

--create index nyc_subway_stations_geom_gist on public.nyc_subway_stations using spgist(point((st_x(geom)),(st_y(geom))) kd_point_ops);

--create index nyc_subway_stations_geom_gist on public.nyc_subway_stations using spgist(point((st_x(geom)),(st_y(geom))) quad_point_ops);


--Geometry Function

--the neighborhood of the ‘Broad St Subway Station’

--SELECT name, ST_AsText(geom) FROM nyc_subway_stations WHERE name = 'Broad St';

--SELECT name, boroname FROM nyc_neighborhoods WHERE ST_Intersects(geom, ST_GeomFromText ('POINT(583571 4506714)',26918));


--Range Query- Lists all the subway stations within a given neighborhood.


--select nyc_subway_stations.name, nyc_neighborhoods.name 
from nyc_subway_stations,nyc_neighborhoods 

where (nyc_neighborhoods.name = 'East Village' or nyc_neighborhoods.name = 'West Village')

and nyc_neighborhoods.geom && nyc_subway_stations.geom;



--Joins

--The following query finds the neighborhood of ‘South Ferry’ subway station.

--set enable_seqscan = off;

--set enable_nestloop = off;

--explain analyze SELECT nyc_subway_stations.name,nyc_neighborhoods.name, nyc_neighborhoods.boroname FROM nyc_neighborhoods JOIN nyc_subway_stations ON ST_Contains(nyc_neighborhoods.geom, nyc_subway_stations.geom);


--k-NN

--k1

--What subway station is nearest to ‘Bensonhurst' neighborhood.

--SELECT s.name, s.routes FROM nyc_subway_stations AS s JOIN nyc_neighborhoods AS n ON ST_Contains(n.geom, s.geom) WHERE n.name = 'Bensonhurst';

--k2

--The closest street to ‘Cortlandt’ subway station.

--SELECT streets.gid, streets.name FROM nyc_streets streets, nyc_subway_stations subways WHERE subways.name = 'Cortlandt St' ORDER BY ST_Distance(streets.geom, subways.geom) ASC LIMIT 1;

