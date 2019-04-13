# Troubleshooting

## Blank page when accessing the page 

> ![troubleshooting](img/error.png) If you have a 404 error, your front might not be compiled on your microservice.
You need to recompile it via the following command: `mvn clean install -P compile-front -pl $MICROSERVICE_NAME`.


## Inconsistency in the database

> ![troubleshooting](img/error.png) If you have an error saying that you have an inconsistency in the database, and something
about hibernate and a result set at 2, you probably need to stop and restart your microservise.


## Issue with running the docker command 

> ![troubleshooting](img/error.png) If you see a permission issue when running the command docker to check if you properly
sent the kafka message, you might need to run the command with **sudo**. (the two commands...)
