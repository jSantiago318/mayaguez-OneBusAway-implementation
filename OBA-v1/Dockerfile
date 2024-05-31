# FROM openjdk:7
FROM openjdk:8u292-jdk

ADD ./bundle/ /bundle/
ADD oba.war /oba.war

CMD java  -Xms1024m -Xmx1G -server -jar /oba.war -webapp \
    -gtfsRealtimeAlertsUrl="" \
        -gtfsRealtimeTripUpdatesUrl="GTFS_REALTIME_TRIP_UDPATES" \
    -gtfsRealtimeVehiclePositionsUrl="GTFS_REALTIME_VEHICLE_POSITION" \
    ./bundle/ 
# CMD ["java", "-jar"]