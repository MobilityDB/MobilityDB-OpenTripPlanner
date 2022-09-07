# MobilityDB-OpenTripPlanner

**Author: Maazouz Mehdi** <br>
**Email: maazouz.mehdi95@gmail.com** <br>

This work represents my contribution to multi-modal routing as part of my thesis at the Université Libre de Bruxelles under the supervision of Professor Esteban Zimanyi. This repository is a clone of the original located [here](https://github.com/MaazouzMehdi/ConnectOTP). You can find a presentation about it at this [address](https://docs.mobilitydb.com/pub/MobilityDB-OpenTripPlanner.pdf) as well as a [detailed workshop](https://github.com/MobilityDB/MobilityDB-OpenTripPlanner/blob/main/workshop/pdf/connect-workshop.pdf).

This project aims to show you how to connect OpenTripPlanner and MobilityDB and to display the generated trips on Qgis. The generated trips are multi-modal and focus on means of transport obtained via GTFS and GBFS data.

OpenTripPlanner (OTP) is an open source multi-modal trip planner, focusing on travel by scheduled public transportation in combination with bicycling, walking, and mobility services including bike share and ride hailing. Its server component runs on any platform with a Java virtual machine (including Linux, Mac, and Windows). It exposes REST and GraphQL APIs that can be accessed by various clients including open source Javascript components and native mobile applications. It builds its representation of the transportation network from open data in open standard file formats (primarily GTFS and OpenStreetMap). It applies real-time updates and alerts with immediate visibility to clients, finding itineraries that account for disruptions and service changes. OTP is released under the LGPL license. As of 2020, the codebase has been in active development for over ten years, and is relied upon by transportation authorities and travel planning applications in deployments around the world ([more information](http://docs.opentripplanner.org/en/latest/)). 

Thanks to the API provided by OpenTripPlanner, we will be able to generate trips according to some desired parameters (time and date we want to leave, desired modes of transport, ...). MobilityDB will allow us to generate moving points to simulate the movement of people.

Modified version Of OpenTripPlanner 2.1
---------------------------------------
This modified version of OpenTripPlanner offers better compatibility with the [GBFS](https://github.com/NABSA/gbfs) format, specifically free_bike_status.json, station_status.json and geofencing_zones.json.
You will find here a list of new cases that are handled by our version of OpenTripPlanner:

* It prevents a user from taking a vehicle already reserved by someone else or already disabled.
* It prevents a user from taking a vehicle already disabled.
* It prevents the user from taking a vehicle that is not sufficiently loaded to make a trip.
* It prevents a user to start a trip from a station out of service, but rather direct it to a nearby and accessible station.
* It prevents a user to end a trip to a station out of service, but rather direct it to a nearby and accessible station.
* It prevents a user to drop off a vehicle in an unauthorised area and/or even to cross an unauthorised area with that vehicle.

Requirements
------------

*   Linux (other UNIX-like systems may work, but remain untested)
*   Java >= 11
*   PostgreSQL >= 11
*   PostGIS >= 2.5
*	Python >= 3.0
	* As well as a compatible version of pip 
*   MobilityDB 1.0 ( or dev branch )
*   JSON-C
*   QGis 3.22 Białowieża and one of its module
	* Move ( https://github.com/mschoema/move )

Optional Dependencies
-----------------------
Dependencies needed for user's documentation :

* The DocBook DTD and XSL files are required for building the documentation. For Ubuntu, they are provided by the packages
`docbook` and `docbook-xsl`.
* The XML validator xmllint is required for validating the XML files of the documentation. For Ubuntu, it is provided by the
package `libxml2`.
* The XSLT processor xsltproc is required for building the documentation in HTML format. For Ubuntu, it is provided by
the package `libxslt`.
* The program dblatex is required for building the documentation in PDF format. For Ubuntu, it is provided by the package
`dblatex`.


Issues 
-----------------------
Please report any [issues](https://github.com/MobilityDB/MobilityDB-OpenTripPlanner/issues) you may have.


Documentation
-------------

### Tutorial

You can generate the tutorial in HTML as well as PDF format. The tutorial is generated in English ( currently working to generate the manual in French as well). For this, you have to run `make_doc.sh` with appropriate options in the cmake command as follows:

*   `-html`: Generate in HTML format
*   `-pdf`: Generate in PDF format

For example, the following command generates the documentation in HTML format.
```bash
bash make_doc.sh -html
```
Note that this file is at the root of the project. Moreover you may need to use `sudo` if `make_doc.sh` causes errors.
The resulting documentation will be generated in the `doc` directory.

In addition, pregenerated version of PDF format is available :

*   [PDF](https://github.com/MobilityDB/MobilityDB-OpenTripPlanner/tree/main/workshop/pdf/connect-workshop.pdf)


Overview
-----------------------
Here you can see an overview of several trips generated by the OpenTripPlanner API and converted into MobilityDB format. This format allows us to visualize temporal points moving over time. This allows us to simulate and follow the trips of people in a city (Brussels in our example)
(The video has been accelerated).

![trips_white](https://github.com/MobilityDB/MobilityDB-OpenTripPlanner/blob/main/images/35Trips.gif?raw=true)

Here you can see a person walking from point A to point B while a second person makes the same journey, at the same time, by public transport
(The video has been accelerated).

![trips_white](https://github.com/MobilityDB/MobilityDB-OpenTripPlanner/blob/main/images/comparisonTrip.gif?raw=true)

Here you can see a person making a trip from point A to point B.
The green dot represents the person walking.
The yellow dot represents the person waiting.
The red dot represents the person making the trip by public transport
(The video has been accelerated).

![trips_white](https://github.com/MobilityDB/MobilityDB-OpenTripPlanner/blob/main/images/advancedtrip.gif?raw=true)

Here you can see a lot of people making trip in Brussels.
The green dot represents people walking.
The yellow dot represents people waiting.
The red dot represents people making the trip by public transport
(The video has been accelerated).

![trips_white](https://github.com/MobilityDB/MobilityDB-OpenTripPlanner/blob/main/images/73advancedtrips.gif?raw=true)

Benchmark
-----------------------

This section is intended to show the maximum number of requests that can be processed per hour. 
This gives us an idea of the scalability of our application.

### Machine used

*  Ubuntu 18.04 LTS 
*  SSD 512 Go 
*  8Go RAM 
*  Intel Core i5-5300U CPU running at 2.30GHz (2 physical cores and 2 logical cores)

### Test 1

For this test, we used OSM data from Brussels and GTFS data from the STIB. Here are the results :

*  Graph building took 1.3 minutes
*  The graph build contains : |V| = 139519
*  The graph build contains |E| = 367902 
*  1 Go was necessery to create the graph

![trips_white](https://github.com/MobilityDB/MobilityDB-OpenTripPlanner/blob/main/images/benchmark1.jpg)

We use multi-trheading to reach more than 27 700 requests per hour which corresponds to 462 queries per minute.
However an user using this application would be limited to Brussels city and a trip composed exclusively of 2 modalities (STIB agency and the walging).

### Test 2

We created a bounding box allowing us to enlarge our OSM data by taking Brussels and its periphery.
We increase our modalities up by 1 (we've got STIB angecy, De Lijn agency and the walking). Here are the results :

*  Graph building took 4.8 minutes
*  The graph build contains : |V| = 232752
*  The graph build contains |E| = 514152 
*  4 Go was necessery to create the graph

![trips_white](https://github.com/MobilityDB/MobilityDB-OpenTripPlanner/blob/main/images/benchmark2.jpg)

Since the graph is larger (|V| increased by 66 % and |E| incresead by 40 %) and more modalities are available, the average
number of queries executed per hour decreases significantly compared to our first test. Our application reach 11965 queries per
hour which corresponds to 200 queries per minute. We observe a 46% reduction in the number of average requests executed by
the application in a given time
