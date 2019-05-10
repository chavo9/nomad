# nomad getting started

## How to use

- Clone the repo:

```
git clone https://github.com/chavo1/nomad.git
cd nomad
vagrant up
vagrant ssh
```
- Verify the installation - you should see help
```
nomad
```
-  Starting the Agent in development mode
```
sudo nomad agent -dev
```
- Open a second console to the virtual machine
```
vagrant ssh
nomad server members
```
The4 output should similar to this one:
```
vagrant@nomad:~$ nomad server members
Name          Address    Port  Status  Leader  Protocol  Build  Datacenter  Region
nomad.global  127.0.0.1  4648  alive   true    2         0.9.1  dc1         global
```
- Stop nomad 'Ctrl-C' and register a job, start nomad and check status
```
nomad job init
sudo nomad agent -dev
nomad status example
```
- An allocation represents an instance of Task Group placed on a node. To inspect an allocation we use the alloc status command
```
nomad alloc status 019048e4(use the ID from previous command)
```
- Checking the log
```
nomad alloc logs 019048e4 redis
```
- Modifying the job - just change count in 'example.nomad' file
```
# The "count" parameter specifies the number of the task groups that should
# be running under this group. This value must be non-negative and defaults
# to 1.
count = 3
```
- Plan the job. The scheduler will detect the change in count and informs us that it will cause 2 new instances to be created.
```
nomad job plan example.nomad
```
- We can then run the job with the run command. By running with the -check-index flag, Nomad checks that the job has not been modified since the plan was.
Because we set the count of the task group to three, Nomad created two additional allocations to get to the desired state. run.
```
nomad job run -check-index 7 example.nomad
```
- Changing the version of redis. Edit the example.nomad file and change the Docker image from "redis:3.2" to "redis:4.0". Plan and run the job with above commands:

```
# Configure Docker driver with the image
config {
    image = "redis:4.0"
}
```
- Stop the job.
```
nomad job stop example
```
### Create Cluster
- config file for the server for example server.hcl.
```
# Increase log verbosity
log_level = "DEBUG"

# Setup data dir
data_dir = "/tmp/server1"

# Enable the server
server {
    enabled = true

    # Self-elect, should be 3 or 5 for production
    bootstrap_expect = 1
}
```
- Start nomad server
```
nomad agent -config /vagrant/server.hcl
```
- Start nomad clients with following commands or create a config files for the clients from [repository here](https://github.com/hashicorp/nomad/tree/master/demo/vagrant) as an example.
```
nomad agent -config /vagrant/client1.hcl
nomad agent -config /vagrant/client2.hcl
```
- Submit a Job 
```
nomad job run example.nomad
```

### Access the UI
- With Nomad running, visit http://localhost:4646 to open the Nomad UI.
