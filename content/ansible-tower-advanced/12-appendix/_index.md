+++
title = "Appendix"
weight = 12
+++

## Setup Considerations

Here are a number of things to consider when planning a clustered Tower
deployment:

  - The PostgreSQL database is a central component of the cluster.
    Ansible Tower is not taking care of availabilty, redundancy or
    replication of the database, this has to be configured "outside" of
    Tower.

  - The number of instances in a cluster should always be an **odd
    number** and a minimum number of three (else it's not a cluster) with
    a maximum of 20. This concerns only "active" instances, not isolated
    nodes.

  - RabbitMQ is a core component, so a lot of the requirements are
    dictated by it. Like e.g. the odd node count for quorum…

  - Typical cluster considerations apply: All nodes need to be able to
    reliably connect to each other, stable address/hostname resolution,
    geographically co-located with reliable low-latency connections
    between instances. Use isolated nodes for remote sites.

  - Remember there is no concept of primary/secondary instance, all
    systems are primary.

### Installing an Ansible Tower Cluster

For initial configuration of a Tower cluster and for adding new
instances the default Ansible installer is used, but the inventory file
needs to be extended. Some important basic concepts:

  - There has to be at least an inventory group named `tower`. We’ll
    cover instance groups later, but keep in mind the nodes in this
    group are responsible for housekeeping tasks like where to launch
    jobs or to process playbook events.

  - If all Tower instances in this group fail, jobs might not run and
    playbook events might not get written. So make sure there are enough
    instances in this group.

  - The database can be installed and configured by the installer by
    adding the host to the `database` group. If the database host is
    provisioned separately, leave the group empty.

### The Installer Inventory File

In this lab a three node Ansible Tower cluster is provided ready to go
as installing it would eat too much of your lab time. It’s pretty
straight forward anyway. The inventory file here is just for giving you
an idea what you are using here and for reference.

{{% notice tip %}}
Keep in mind when working with clustered Ansible Tower that the database will not be clustered or replicated by the installer. This is something you would have to take care of yourself.
{{% /notice %}}

    [tower]
    tower1.example.com
    tower2.example.com
    tower3.example.com

    [database]
    towerdb.example.com

    [all:vars]
    ansible_become=true

    admin_password='{{< param "secret_password" >}}'

    pg_host='towerdb.example.com'
    pg_port='5432'

    pg_database='tower'
    pg_username='tower'
    pg_password='{{< param "secret_password" >}}'

    rabbitmq_port=5672
    rabbitmq_vhost=tower
    rabbitmq_username=tower
    rabbitmq_password='{{< param "secret_password" >}}'
    rabbitmq_cookie=rabbitmqcookie

{{% notice warning %}}
In this lab this has been taken care of for you, but remember: all instances have to be able to resolve all hostnames and to reach each other\!
{{% /notice %}}

# The End

Congratulations, you finished your labs\! We hope you enjoyed your encounter with some advanced features of Ansible Tower as much as we enjoyed creating the labs.
