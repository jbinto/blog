---
layout: post
title: "Loading Toronto Bikeways data into PostGIS"
date: 2014-03-24 14:38:12 -0400
comments: true
categories: 
---

## Working with shapefiles

I've been working with the [City of Toronto's open data catalogue](http://toronto.ca/open), specifically the Bikeways route data. The route data is provided as an [ESRI Shapefile](https://en.wikipedia.org/wiki/Shapefile), a once proprietary format, now an open standard.

If you go to the Toronto open data site, you'll see there are two shapefiles provided: "MTM 3 Degree Zone 10, NAD27" and "WGS84 (Latitude/Longitude)". After [a short crash course on coordinate systems and projections](http://daniel-azuma.com/articles/georails/part-4), I found I'm in luck: WGS84 is what Google Maps uses, so the data is already in the correct format.

There are open source tools you can use to convert from shapefiles to something more usable, such as [KML](https://en.wikipedia.org/wiki/Keyhole_Markup_Language) (if you've ever used Google Earth or the Maps API, you're familiar with KML). There's also a newer format called [GeoJSON](https://en.wikipedia.org/wiki/GeoJSON), which I'll be using. In a stroke of luck, the Google Maps team just recently (March 2014) announced support for GeoJSON, through it's new [Data Layer](https://developers.google.com/maps/documentation/javascript/datalayer) functionality.

The first naive approach I took was to just dump the whole file into GeoJSON.

I followed [these instructions](http://ben.balter.com/2013/06/26/how-to-convert-shapefiles-to-geojson-for-use-on-github/) and used the `ogr2ogr` program which is part of the [GDAL](http://www.gdal.org/index.html) suite of tools. On OS X:

{% codeblock %}
brew install gdal
ogr2ogr -f GeoJSON -t_srs crs:84 bikeways.geojson CENTRELINE_BIKEWAY_OD_WGS84.shp
{% endcodeblock %}

It didn't take long, less than a second, and I was surprised not to be confronted with a wall of verbose text. I pushed it to Github. They render GeoJSON commits in a map view in the browser, so lacking any other tools to verify this output, I figured it'd be a good smoke test. What I didn't realize was that the file was 50MB, not the 5MB I thought at first glance. The browser spun and spun, but eventually timed out and couldn't render the file.

## Installing PostGIS

Clearly, it would take a bit more work. [I got to reading about PostGIS](http://workshops.boundlessgeo.com/postgis-intro/), which is an spatial / geographical database extension for PostgreSQL.

I already had PostgreSQL installed via homebrew. I haven't made any notable changes to the configuration. PostGIS can also be installed via Homebrew:

{% codeblock %}
brew install postgis
createdb postgis_junk
psql -d postgis_junk
{% endcodeblock %}

{% codeblock lang:sql %}
CREATE EXTENSION postgis;

-- verify the extension is installed
SELECT postgis_full_version();
{% endcodeblock %}

## Importing the data

I then used the `shp2pgsql` tool to generate an SQL script to load the data into PostgreSQL. 

{% codeblock %}
# -s = source projection. WGS84 is also known as EPSG:4326.
# -I = index the geometry column
# -c = create new
shp2pgsql -s 4326 -I -c -W UTF-8 CENTRELINE_BIKEWAY_OD_WGS84.shp shapes > shapes.sql

wc -l shapes.sql
60444
{% endcodeblock %}

60,000 INSERT statements! At this point, I had no idea what the data looked like. The [Toronto cycling map](http://www1.toronto.ca/wps/portal/contentonly?vgnextoid=42b3970aa08c1410VgnVCM10000071d60f89RCRD) has a lot going on, but there's certainly not 60,000 routes.

I loaded the data into PostgreSQL and started looking at it.

{% codeblock %}
psql -d postgis_junk < shapes.sql
echo "\d shapes" | psql -d postgis_junk

                                       Table "public.shapes"
   Column   |              Type              |                      Modifiers                       
------------+--------------------------------+------------------------------------------------------
 gid        | integer                        | not null default nextval('shapes_gid_seq'::regclass)
 geo_id     | numeric(10,0)                  | 
 lfn_id     | numeric(10,0)                  | 
 lf_name    | character varying(110)         | 
 address_l  | character varying(20)          | 
 address_r  | character varying(20)          | 
 oe_flag_l  | character varying(2)           | 
 oe_flag_r  | character varying(2)           | 
 lonuml     | integer                        | 
 hinuml     | integer                        | 
 lonumr     | integer                        | 
 hinumr     | integer                        | 
 fnode      | numeric(10,0)                  | 
 tnode      | numeric(10,0)                  | 
 one_way_di | smallint                       | 
 dir_code_d | character varying(20)          | 
 fcode      | integer                        | 
 fcode_desc | character varying(100)         | 
 juris_code | character varying(20)          | 
 objectid   | numeric                        | 
 cp_type    | character varying(50)          | 
 rid        | double precision               | 
 geom       | geometry(MultiLineString,4326) | 
Indexes:
    "shapes_pkey" PRIMARY KEY, btree (gid)
    "shapes_geom_gist" gist (geom)
{% endcodeblock %}

The data will need to be cleaned up significantly. It's denormalized with a lot of repetition.

<a href="/images/bikeways_denorm.png">{% img /images/bikeways_denorm.png 833 420 A sample of the data, straight from shp2pgsql %}</a>

Immediately, I noticed some entries that were clearly not bike paths. 427/Gardiner, Brown's Line, etc. It didn't take long to figure out the common thread: `cp_type`. 

{% codeblock %}
select count(*) from shapes;
 60415

select count(*) from shapes where cp_type is not null;
  6573

select distinct cp_type from shapes order by 1;
 Bike Lanes
 Contra-Flow Bike Lanes
 Cycle Tracks
 Informal Dirt Footpath
 Major Multi-use Pathway
 Minor Multi-use Pathway
 Park Roads Cycling Connections
 Sharrows
 Signed Routes
 Suggested On-Street Connections
 Suggested On-Street Routes
{% endcodeblock %}

Well, that's a big difference: 60,000 entries to 6,500 entries. It seems the Toronto open dataset also includes all of the roads in the city. I suppose this makes sense, as in order to make a cycling map you would still need to include the roads that are not specifically designated for cycling.

I considered deleting the roads, but they may serve some purpose to me in the future. I created a view to keep the roads out of the way, to focus entirely on bikeways.

{% codeblock lang:sql %}
CREATE VIEW "public"."bikeways" AS SELECT shapes.gid,
    shapes.geo_id,
    shapes.lfn_id,
    shapes.lf_name,
    shapes.address_l,
    shapes.address_r,
    shapes.oe_flag_l,
    shapes.oe_flag_r,
    shapes.lonuml,
    shapes.hinuml,
    shapes.lonumr,
    shapes.hinumr,
    shapes.fnode,
    shapes.tnode,
    shapes.one_way_di,
    shapes.dir_code_d,
    shapes.fcode,
    shapes.fcode_desc,
    shapes.juris_code,
    shapes.objectid,
    shapes.cp_type,
    shapes.rid,
    shapes.geom
   FROM shapes
  WHERE (shapes.cp_type IS NOT NULL);
{% endcodeblock %}

## Some fun queries

Now, for some simple analysis of the data.

### Toronto bikeways, by name, sorted by length

{% codeblock lang:sql %}
select lf_name, sum(st_length(geom::geography)) from bikeways group by lf_name order by 2 desc;

Humber River Recreational Trl 32281.7240352214
Beltline Trl  18177.9734050018
West Highland Creek Trl 17814.6034264817
Martin Goodman Trl  17091.7664842414
East Don River Trl  14531.8038715227
Finch Corridor Trl  14517.1523456954
...
{% endcodeblock %}

### Toronto bikeways, by type, sorted by length

{% codeblock lang:sql %}
select cp_type, sum(st_length(geom::geography))/1000 as km from bikeways group by cp_type order by 2 desc;

Major Multi-use Pathway 195.97070786872
Signed Routes 132.051380133788
Minor Multi-use Pathway 127.11080747771
Suggested On-Street Routes  125.16581425177
Bike Lanes  109.282966609882
Suggested On-Street Connections 78.4045134911026
Park Roads Cycling Connections  27.6211172379201
Sharrows  9.19191499632497
Informal Dirt Footpath  3.93281102346339
Cycle Tracks  2.65004176773446
Contra-Flow Bike Lanes  2.47592665715739
{% endcodeblock %}


## I still don't fully understand projections

Disclaimer: I've been using PostGIS for less than a week.

PostGIS can store both *[geometry](http://postgis.net/docs/GeometryType.html)* and *[geography](http://postgis.net/docs/manual-1.5/ch04.html#PostGIS_Geography)*. Geometry is as we learned in grade school: 2 dimensional cartesian coordinates, simple to manipulate, familiar formulas for length, area, etc. Geography uses latitude and longitude, which are 3-dimensional spherical coordinates. Asking a `geography` for it's length will return an answer in `degrees`, whereas a `geometry` responds in `metres`. Simply put, the two don't really mix.

Some things I've learned:

* A geography is the "only" way to store latitude/longitudes natively without projections.
* A geometry can only be positioned on the globe when combined with a [SRID](https://en.wikipedia.org/wiki/SRID).
* Geometry has more features than geography and is much faster.
* Geography is more appropriate for doing "global" calculations, e.g. trans-oceanic flight paths.
* Geometry is more appropriate for city, province/county level calculations

Here's where it gets messy. One thing that worries me is the following quote:

{% blockquote Daniel Azum http://daniel-azuma.com/articles/georails/part-7 GeoRails: Geometry vs. Geography, or, How I Learned To Stop Worrying And Love Projections %}
You might be tempted to store latitude and longitude in a geometry type column. That is, to set up your PostGIS column with a geometry type, but use SRID=4326 (which is the EPSG number for WGS 84 latitude and longitude).

Don't do this.
{% endblockquote %}

I'm pretty sure that's *exactly* what I'm doing. It'll take me a bit longer to understand exactly what this means and why it's a problem, because for now, everything is working fine.

Next step: Creating a Rails app, with migrations to automate the shapefile import and associated data normalization.
