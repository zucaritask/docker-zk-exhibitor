### *Note*: Fork of mbabineau/docker-zk-exhibitor to accomodate for our needs
  - Default s3backups disabled
  - ZK JMX monitoring port 8250
  - Default AWS region eu-west-1

Runs an [Exhibitor](https://github.com/Netflix/exhibitor)-managed [ZooKeeper](http://zookeeper.apache.org/) instance using S3 for backups and automatic node discovery.

Available on the Docker Index as [zucaritask/docker-zk-exhibitor](https://index.docker.io/u/zucaritask/docker-zk-exhibitor/):

    docker pull zucaritask/docker-zk-exhibitor

### Versions
* Exhibitor 1.5.5
* ZooKeeper 3.4.6

### Usage
The container expects the following environment variables to be passed in:

* `HOSTNAME` - addressable hostname for this node (Exhibitor will forward users of the UI to this address)
* `S3_BACKUP` - (optional) use S3 backup (defaults to false)
* `S3_BUCKET` - (optional) bucket used by Exhibitor for backups and coordination
* `S3_PREFIX` - (optional) key prefix within `S3_BUCKET` to use for this cluster
* `AWS_ACCESS_KEY_ID` - (optional) AWS access key ID with read/write permissions on `S3_BUCKET`
* `AWS_SECRET_ACCESS_KEY` - (optional) secret key for `AWS_ACCESS_KEY_ID`
* `AWS_REGION` - (optional) the AWS region of the S3 bucket (defaults to `us-west-2`)
* `ZK_PASSWORD` - (optional) the HTTP Basic Auth password for the "zk" user
* `ZK_DATA_DIR` - (optional) Zookeeper data directory
* `ZK_LOG_DIR` - (optional) Zookeeper log directory
* `HTTP_PROXY_HOST` - (optional) HTTP Proxy hostname
* `HTTP_PROXY_PORT` - (optional) HTTP Proxy port
* `HTTP_PROXY_USERNAME` - (optional) HTTP Proxy username
* `HTTP_PROXY_PASSWORD` - (optional) HTTP Proxy password

Starting the container:

    docker run -p 8181:8181 -p 2181:2181 -p 2888:2888 -p 3888:3888 -p 8250:8250 \
        -e S3_CONFIG=<true/false> \
        -e S3_BUCKET=<bucket> \
        -e S3_PREFIX=<key_prefix> \
        -e AWS_ACCESS_KEY_ID=<access_key> \
        -e AWS_SECRET_ACCESS_KEY=<secret_key> \
        -e HOSTNAME=<host> \
        zucaritask/docker-zk-exhibitor:latest

Once the container is up, confirm Exhibitor is running:

    $ curl -s localhost:8181/exhibitor/v1/cluster/status | python -m json.tool
    [
        {
            "code": 3, 
            "description": "serving", 
            "hostname": "<host>", 
            "isLeader": true
        }
    ]
_See Exhibitor's [wiki](https://github.com/Netflix/exhibitor/wiki/REST-Introduction) for more details on its REST API._

You can also check Exhibitor's web UI at `http://<host>:8181/exhibitor/v1/ui/index.html`

Then confirm ZK is available:

    $ echo ruok | nc <host> 2181
    imok
