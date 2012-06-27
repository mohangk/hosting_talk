title: Hosting in the Lion City for Fun and Profit
author: Mohan Krishnan 

## 2 parts

1. ### Comparison of cloud providers in SG
2. ### How would we build our own Heroku

## Hosting options

### We looked at

#### IAAS
 1. EC2
 2. Softlayer
 3. Voxel

#### PAAS
 1. EngineYard (uses EC2)

## Method

### Using Heroku as the baseline
  - #### 3 dynos (2 web + 1 worker)
  - #### DB (Crane)
  - #### Addons: Redis, SSL, Memcache
  - #### Assumptions
    - 1 Dyno ~ micro to small instance, 512MB 
    - Small instance: 1 EC2 Compute Unit ~ 1-1.2 GHz 2007 Xeon, 1.7GB

## What we compared ?
  - Price
  - System setup
  - Ease of app deployment
  - Scaling
  - Monitoring
  - Customisation
  - Adding redundancy

## TLDR;

### Takeaways
  - Not many options
  - Heroku is very competitive - sweet spot: small sites
  - EC2 reserved instances gets interesting for large deployments 
  - I/O speeds can be a huge differential amongst providers [1]
  - Softlayer / Voxel offer hybrid options - webservers on cloud with db on dedicated boxes

### Questions?
  - How would we factor cost of ongoing system maintenance ?
  - Is it safe to run databases on [cloud](http://it.toolbox.com/blogs/database-soup/how-to-make-your-database-perform-well-on-amazon-ec2-45725)
 [servers](http://postgresql.1045698.n5.nabble.com/amazon-ec2-td4368036.html) ? 

## Part 2 - Building our own Heroku

### What does Heroku provide?
 - Setup of app server/database stack
 - Maintenance of stack
 - Easy deploy 
 - Easy scaling
 - HTTP based API
 - Backup 
   - Appserver backup
   - DB 
     - Continuous backup - WAL archiving
     - Follow - replication, manual failover

## The pieces

###Setup

   - [chef-rack-stack](http://github.com/newcontext/chef-rack_stack)
   - [easy_rack_stack](http://github.com/newcontext/easy_rack_stack)

###Deploy

   - [git-deploy](http://github.com/newcontext/git-deploy)
    

## [chef-rack-stack](http://github.com/newcontext/chef-rack_stack)

### What is it ?
- Chef cookbook that 
  - Simplicity first - minimal configuration, one app per instance
  - Install necessary dependencies: Apache, mod_passenger, RVM, Postgres, Redis, Memcache
  - Sets up deploy user, uploads public key
  - Sets up a Apache site with an empty app folder, ready for deployment
- Uses existing community cookbooks
- Works with both Vagrant and EC2- Ubuntu 12.04 image

### Going forward
- Add tests ?
- More modular 
  - Break into roles, web, database, cache, redis 
- Support nginx and other Rack hosts (unicorn, thin) 

## [chef-rack-stack](http://github.com/newcontext/chef-rack_stack)

### Takeaways
 - Finding suitable cookbooks are not straighforward
  - SRC install or package
  - Play well with other cookbooks
  - Customisability
 - Manual dependency management is a pain - Use [Librarian](https://github.com/applicationsonline/librarian)
 - Use [Foodcritic](http://acrmp.github.com/foodcritic/) as part of a CI process
 - Community hackers with well written cookbooks 
   - [Fletcher Nichol](https://github.com/fnichol)
   - [Eric G. Wolfe](https://github.com/atomic-penguin)
   - [Coroutine](https://github.com/coroutine)
 - Is it worth building/collating/continuosly test a set of recipes + server image that we would use for NC projects when we don't use Heroku ?

## [easy_rack_stack](http://github.com/newcontext/easy_rack_stack)

### What is it ?
- Chef cookbook that 
  - Simplicity first - minimal configuration, one app per instance
  - Install necessary dependencies: Apache, mod_passenger, RVM, Postgres, Redis, Memcache
  - Sets up deploy user, uploads public key
  - Sets up a Apache site with an empty app folder, ready for deployment
- Uses existing community cookbooks
- Works with both Vagrant and EC2- Ubuntu 12.04 image

### Going forward
- Add tests ?
- More modular 
  - Break into roles, web, database, cache, redis 
- Support nginx and other Rack hosts (unicorn, thin) 



## Interestings

Vagrant 2.0

### [Asgard](http://techblog.netflix.com/2012/06/asgard-web-based-cloud-management-and.html)

### [WALL-E](https://github.com/heroku/wal-e)



[1] http://blog.cloudharmony.com/2010/06/disk-io-benchmarking-in-cloud.html
[2] [3] [4]
