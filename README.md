# Dataspout

A simple application with input and output filter which can move data from the source to sink.

This project originally came from a realisation that the **docker.sock** provides a large quantity of information. The answer to one of those sources in the form of **Logspout** which transfers logs from docker to a destination (of which there are many).

So why not extend Logspout?

I added a module to output to Kafka in the JSON format (based on the existing json redis and kafka outputs). The problem is that it would be nice to change the data sources as well and data describes the aim far better than log. In addition some data like docker stats require timed events and possibly aggregation. Something that goes well beyond the existing use case for Logspout.

There is also a lot of fragmentation in the implementations of Logspout because the plugins do not run in a centralised way (i.e 15 Kafka plugins all doing similar but 
ultimately different things). The modular nature is apprecitated but forks dont work well for pull requests/issues/etc

This project is not in competition with logspout. Its an attempt to get data from a to b. If this functionality ever gets implemented into logspout neatly as an input I would happily give this repo away to the maintainers of Logspout.

Most importantly if anywhere you have seen an open source project like this somewhere else please say something. It would save a lot of wasted time.

#### Implementation Details

This project will be implemented in go because it can be small, fast and lightweight. Perfect for docker requirements.

All configuration would be done via environment variables to ensure friendliness with orchestrators such as Marathon and Kubernetes.

The plan would be to have a docker hub docker image containing all the common filters which would be turned on just by configuring the destination. The idea being to remove the technical knowledge needed for a simple usage. Plugins would be contained in **input** and **output** directories and new plugins would be implemented on branches which would be merged via the PR mechanism into the main project.

#### Input filters

|Filter name | Source | Usage |
|----|--------|-------|
|DockerHostMonitoring|/proc, /sys and /var|Parent host monitoring|
|DockerContainerLogs|Docker socket|Log ouput (similar to logspout)|
|DockerContainerStats|Docker socket|Stats output (CPU, Network, Disk I/O, Memory)
| Example |Docker socket|Container introspection<br><li>JMX for Java within a container<br><li>NodeJS stats<br><li>etc

   
#### Output filters

| Filter Name | Usage |
|--------|-------|
|Kafka|Ideally the output should be normalised to allow for the avro format (if possible)|
|Stdout|The king of debuging destinations|

Other filters could be created as required.

#### Inspiration

   It is clear when you have a lot of data that Kafka is an excellent way to transport it. It is also clear that using containerisation is a trend that will not stop. By mixing these use cases we can form a generic transport mechanism to automatically instrument hosts (both the container and underlying).
   
   Planned consumers for the output would be Druid (for stats) with Grafana for visualisation and Elasticsearch (for text based output like logs)
   
#### Contributions

   Yes, please do. Input filters, Output filter, Core code, Docs. PR requests should be an obligation for people using this project.
   
   In addition there will be no remorse in stealing from other open source projects and every attempt will be made to credit them appropriately. We will try and match up licenses appropriately or ask for permission.
   
   Issues are also valued in the same way. If you find something broken please raise an issue or fix it and submit a PR. If you want to be a core maintainer. Submit a few PR's and you'll soon get promoted.

#### Credits

   * Logspout (an perfect example of how to do a hard job in a simple way)