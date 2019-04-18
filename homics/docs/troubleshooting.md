# Troubleshooting

## Blank page when accessing the website

> ![troubleshooting](img/error.png) If you have a 404 error, your front might not be compiled on your microservice.
You need to recompile it via the following command: `mvn clean install -P compile-front -pl $MICROSERVICE_NAME`.


## Inconsistency in the database

> ![troubleshooting](img/error.png) If you have an inconsistency in the database (something about hibernate and a _resultSet_
at 2, you can try to restart your microservice. If it does not work, try to recompile everything with the front and restart
the service.


## Issue with running the docker command 

> ![troubleshooting](img/error.png) If you see a permission issue when running the command docker to check if you properly
sent the kafka message, you might need to run the command with **sudo**. (Don't forget to add the sudo on both docker commands).
