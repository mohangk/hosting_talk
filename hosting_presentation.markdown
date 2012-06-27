2 parts
=======

- Comparison of cloud hosting provider which have data centres in SG?
- Assuming we go with a IAAS provider (e.g. EC2 ) what tooling can
we build use to make the experience as seamless as Heroku

Hosting options
===============

We looked at

- IAAS
 - EC2
 - Softlayer
 - Voxel

- PAAS
 - EngineYard (uses EC2)

- Use heroku as a benchmark - estimated dyno ~ small instance
- Compared
 - system setup,
 - ease of deployment,
 - scaling,
 - monitoring,
 - Ease of customisations,
 - Redundancy

TLDR;
-----
- There are not too many options for cloud hosting providers in SG
- Heroku is very competitive, for small amount of dynos + minimal add ons
- Reserved instances really drive EC2 prices down and hence make it extremely interesting for large deployments, where the cost of setup and maintenance can be absorbed
- Ongoing system admin and maintenance might require some type of continuous infrastructure setup and upgrade testing
- I/O speeds can be a huge differential amongst providers - can be an issue for database servers [1]
- Softlayer / Voxel offer hybrid options - webservers on cloud with db on dedicated box ?

Part 2 - Build our own Heroku
=============================

- Heroku provides

 -setup of app server/database stack
 - maintenance of stack
 - scaling
 - appserver backup
 - db continuous backup/replication, manual failover


Interestings
------------

Vagrant 2.0


Asgard

[1] http://blog.cloudharmony.com/2010/06/disk-io-benchmarking-in-cloud.html
http://techblog.netflix.com/2012/06/asgard-web-based-cloud-management-and.html