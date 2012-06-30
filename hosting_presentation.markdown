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
  - [I/O speeds](http://blog.cloudharmony.com/2010/06/disk-io-benchmarking-in-cloud.html) can vary significantly between providers
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
  - Puts simplicity first - minimal configuration, one app per instance
  - Install necessary dependencies for typical Rails app: Apache, mod_passenger, RVM, Postgres, Redis, Memcache, nodejs, RMagick
  - Sets up deploy user, uploads public key
  - Sets up a Apache site with an empty app folder, ready for deployment
- Uses existing community cookbooks
- Works with both Vagrant and EC2- Ubuntu 12.04 image

## [chef-rack-stack](http://github.com/newcontext/chef-rack_stack)

### Going forward
- Housekeeping: README.md, metadata.rb 
- Add tests ? Use [Foodcritic](http://acrmp.github.com/foodcritic/)  
- Support nginx and other Rack hosts (unicorn, thin) 
- More modular 
  - Break into roles, web, database, cache, redis 

## [chef-rack-stack](http://github.com/newcontext/chef-rack_stack)

### Takeaways
 - Finding suitable cookbooks are not straighforward
   - SRC install or package
   - Play well with other cookbooks
   - Customisability
 - Manual dependency management is a pain - Use [Librarian](https://github.com/applicationsonline/librarian)
 - Use [Foodcritic](http://acrmp.github.com/foodcritic/) as part of a CI process
 - Hackers with well written cookbooks 
   - [Fletcher Nichol](https://github.com/fnichol)
   - [Eric G. Wolfe](https://github.com/atomic-penguin)
   - [Coroutine](https://github.com/coroutine)
 - Should NC maintain a set of recipes + server image that we would use for non Heroku deploys ? 

## [easy_rack_stack](http://github.com/newcontext/easy_rack_stack)

### What is it ?
- A collection of shell scripts and config templates that will grow up to be a command line tool
- Provides a Vagrantfile that uses chef-rack_stack recipe
- Provides a Cheffile with relevant dependencies
- Provides a setup.sh and bootstrap.sh script that helps spins up an EC2 instance and run the same chef setup as per the Vagrantfile
- Adapted from existing effort by [Nicolas Rakato](https://github.com/nrako/librarian-ec2)

## [easy_rack_stack](http://github.com/newcontext/easy_rack_stack)

### For use with Vagrant
    git clone git://github.com/newcontext/easy_rack_stack.git ~/workspace/ers
    cp ~/workspace/ers/Vagrantfile ~/workspace/your_project
    cp ~/workspace/ers/Cheffile ~/workspace/your_project
    cd ~/workspace/your_project
    echo "gem 'vagrant'" >> Gemfile
    echo "gem 'librarian'" >> Gemfile
    bundle
    librarian-chef install
    vagrant box add precise64 http://files.vagrantup.com/precise64.box
    vagrant up
  
## [easy_rack_stack](http://github.com/newcontext/easy_rack_stack)

### For use with EC2
    <song and dance of setting up the EC2 CLI API tools>
    <song and dance of setting up an EC2 keypair>
    ec2-run-instances ami-2ab8fe78  --key ec2-ap-southeast-1-keypair --user-data-file bootstrap.sh
    ec2-describe-instances
    ./setup.sh IP_ADDRESS ./ ~/.ec2/ec2-ap-southeast-1-keypair

## [easy_rack_stack](http://github.com/newcontext/easy_rack_stack)

### Takeaways
- When developing apps for EC2 deployments
  - database.yml does matter
  - Use ENV vars in config files as much as possible
  - `config/setup_load_paths.rb` required for Passenger

### Going forward
- Update README.md
- Make into a command line tool and a Gem
- Automate the EC2 instance boot (or any other cloud provider)
- Allow for more complex setups
  - Separete app/db setup when setting up on EC2 

### Questions ?
- Would we consider using Vagrant ?
- Is dev/prod parity critical ?

## [git-deploy](http://github.com/newcontext/git-deploy)

### What is it ?
- A command line tool that when used in conjuction with git
  - Prepares an empty repo on a designated URL
  - Adds project level post commit hooks that are called when doing a push
  - Restart server
  - Rollback changes
  - Manage environment variables
  - Tail log
- Works over SSH
- Adapted from existing project by [Mislav Marohnić](https://github.com/mislav/git-deploy)

## [git-deploy](http://github.com/newcontext/git-deploy)

### Deploying your app
    gem install git-deploy
    git remote add production user@example.com:/path/to/myapp
    git deploy setup -r production
    git deploy init
    git push production master 

### Default commit hooks
    bundle install --deployment
    rake assets:clean
    rake assets:precompile
    #Restarts application

## [git-deploy](http://github.com/newcontext/git-deploy)

### Going forward 
- Complete tests
- Allow for scaling app servers
- Support for nginx

### Questions ?
- Would capistrano be better suited ?

## Interestings

### [Asgard](http://techblog.netflix.com/2012/06/asgard-web-based-cloud-management-and.html)
 - [https://github.com/Netflix/asgard](https://github.com/Netflix/asgard)

### [WALL-E](https://github.com/heroku/wal-e)

### Vagrant 2.0 - EC2 provider ?

## 

#Terima kasih
