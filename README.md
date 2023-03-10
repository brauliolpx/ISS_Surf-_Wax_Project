Surf Wax ISS Locator: Real-Time Tracking System

This project builds a flask application using an abundance of interesting positional and velocity data for the International Space Station (ISS). The goal is to create a Flask application that queries and returns useful information from the ISS data collection.
This folder contains a flask python script "iss_tracker.py" where the app is build & contains a "dockerfile" to containerize the iss_tracker.py script and a docker-compose.yml to automate the deployment of the app

iss_tracker.py: The Flask application for querying the velocity and position of the International Space Station. The program should load the data from the URL below and provide it to the user using properly constructed routes.
URL: https://nasa-public-data.s3.amazonaws.com/iss-coords/current/ISS_OEM/ISS.OEM_J2K_EPH.xml
The first route '/' returns the entire data set
'/epochs' returns a list of all Epochs in the data set
'/epochs/<epoch>' returns the state vectors for a specific Epoch from the data set
'/epochs/<epoch>?limit=int&offset=int' returns modified list of Epochs given query parameters
'/epochs/<epoch>/speed' Instantaneous speed for a specific Epoch in the data set
'/epochs/<epoch>/location' returns latitude, longitude, altitude, and geoposition for given Epoch
'/help' Return help text (as a string) that briefly describes each route
'/delete-data' it deletes all data from the dictionary object
'/post-data' reloads the dictionary object with data from the web
'/comment' it returns ‘comment’ list obejct from ISS data
'/header ' it returns ‘header’ dict object from ISS data
'/metadata' it returns ‘metadata’ dict object from ISS data
'/now' it return latitude, longitude, altidue, and geoposition for Epoch that is nearest in time

Dockerfile: Dockerfile to containerize iss_tracker.py script runing on python 3.8.10
The run commands are the following:
RUN pip install Flask==2.2.2
RUN pip install xmltodict==0.13.0
RUN pip install requests==2.22.0
COPY iss_tracker.py /iss_tracker.py
CMD ["python", "iss_tracker.py"]


docker-compose.yml contains the following 
services:
    flask-app:
        build:
            context: ./
            dockerfile: ./Dockerfile
        ports:
              - 5000:5000
        image: brauliolpx/iss_tracker:project
        volumes:
            - ./config.yaml:/config.yaml


To access the data just copy the URL above to your a search engine

--------------------
Installation

First you will need to install the requirements the script neeeds in your terminal.
The Flask library is not part of the Python standard library but can be installed with standard tools

Terminal: pip3 install --user flask

You should get a 'Successfully installed flask-2.x.x' message

Once you installed flask you need to open a new terminal tab because you need to see that the flass application is running.
On the terminal type:

Terminal: flask --app iss_tracker --debug run
The expected output should be
* Serving Flask app 'app'
* Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
* Running on http://127.0.0.1:5000
Press CTRL+C to quit
* Restarting with stat
* Debugger is active!
* Debugger PIN: 268-620-354

RUNNING THE ROUTES

now on the second tab you can curl each route to see the outcomes
if you curl localhost:5000/ in the terminal it will list all the data set gotten from the link and return a dictionary containging all of the informartion.
By curl localhost:5000/epochs it will list all epochs from the data set. For example...
...
"2023-063T11:55:00.000Z",
"2023-063T11:59:00.000Z",
"2023-063T12:00:00.000Z"

each epoch is a dictionary containing its state vectors

By curl localhost:5000/epochs?limit=int&offset=int will return the epochs with query parameters of limits and offset.
/epochs?limit=20&offset=50 would return Epochs 51 through 70 (20 total).

If you want to access an specific epoch get the time you want the route is /epochs/<epoch> for this case I will show the last one.
By curl localhost:5000/epochs/2023-063T12:00:00.000Z that will return the X,Y,Z coordinates and its dot velocities. For example:
X: 2820.04422055639 km,
Y: -5957.89709645725 km,
Z: 1652.0698653803699 km,
X_DOT: 5.0375825820999403 km/s,
Y_DOT: 0.78494316057540003 km/s,
Z_DOT: -5.7191913150960803 km/s

By choosing an specific time it will return each state Vectors from the data set

the route curl localhost:5000/epochs/<epoch>/speed gets the speed for that specific epoch value for example the last epoch data set
curl localhost:5000/epochs/2023-063T12:00:00.000Z/speed
returns:
Instantaneous speed is: 7.661757196327827 km/s
That shows the instantaneous speed for the specific epoch value.


the route /epochs/<epoch>/location gets latitude, longitude, altitude, and geoposition for given Epoch example:
curl 127.0.0.1:5000/epochs/2023-080T15:02:07.856Z/location
latitude is: -12.263224927225956 km,
longitude is: 18.736069433860337 km,
altitude is: 429.6314323514889 km,
The geoposition is: Moxico Province, Angola


curl localhost:5000/comment 
Return ‘comment’ list obejct from ISS data


curl localhost:5000/header
returns a dictionary as 
{
  "CREATION_DATE": "2023-066T03:37:31.258Z",
  "ORIGINATOR": "JSC"
}

curl localhost:5000/metadata
{
  "CENTER_NAME": "EARTH",
  "OBJECT_ID": "1998-067-A",
  "OBJECT_NAME": "ISS",
  "REF_FRAME": "EME2000",
  "START_TIME": "2023-065T15:02:07.856Z",
  "STOP_TIME": "2023-080T15:02:07.856Z",
  "TIME_SYSTEM": "UTC"
}

curl localhost:5000/now
Return latitude, longitude, altidue, and geoposition for Epoch that is nearest in time
As a dicitonary 

curl localhost:5000/help
Returns a help parapgrah with the routes the user is able to do

To use the other teo routes we have to put a -X and the its method for example:
curl -X DELETE localhost:5000/delete-data
	All data deleted successfull

curl -X POST localhost:5000/post-data
"message": "Data restored successfully"

-------------------------
Dockerfile  && Docker Hub

First you will need to login
Terminal: docker login
username: **
password: **

After sign in you can pull the docker image from
docker pull brauliolpx/iss_tracker:hw05

To run the image type
Terminal: docker run -it --rm -p 5000:5000 brauliolpx/iss_tracker:hw05
The intended output is
 * Serving Flask app 'iss_tracker'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://172.17.0.2:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 128-187-211

There it shows that the dockerfile is running as expected and you can curl each routes as the same from the flask application

You could also build a new image from the Dockerfile by adding new ports
After doing that just

docker build -t brauliolpx/iss_tracker:hw05

and the new build image would run as expected
---------------------------------------
Docker compose Installation 

when having the composer just type on terminal

terminal: docker-compose up

the expected output should be 

Starting iss_surf-_wax_project_flask-app_1 ... done
Attaching to iss_surf-_wax_project_flask-app_1
flask-app_1  |  * Serving Flask app 'iss_tracker'
flask-app_1  |  * Debug mode: on
flask-app_1  | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
flask-app_1  |  * Running on all addresses (0.0.0.0)
flask-app_1  |  * Running on http://127.0.0.1:5000
flask-app_1  |  * Running on http://172.18.0.2:5000
flask-app_1  | Press CTRL+C to quit
flask-app_1  |  * Restarting with stat
flask-app_1  |  * Debugger is active!
flask-app_1  |  * Debugger PIN: 361-886-249


That means that it is atomitize to run the code and access its routes 


-------------------
UNDERSTANDING ISS DATA

The iss data cointains a dictionary with state vectors that constantly gets updated with more

Each State Vector cointains 4 pices of information the EPOCH, The cartesian cordinates and the velocity components

For example a snipet of the first state vector:

<EPOCH>2023-055T12:00:00.000Z</EPOCH>
 <X units="km">4729.1451892081404</X>
<Y units="km">-3721.63418574457</Y>
<Z units="km">3155.3624474731901</Z>
 <X_DOT units="km/s">5.32121083006865</X_DOT>
 <Y_DOT units="km/s">2.6384668365168999</Y_DOT>
 <Z_DOT units="km/s">-4.8395512800929001</Z_DOT>

This application helps get specific data from this large data set and also access to each state Vector and take a closer look to its components
