!SLIDE

# ** CHEF ** #

!SLIDE center

# ![BORK](swedish_chef_bork-sleeper-cell.jpg) #

!SLIDE

# I don't think you need me to explain the what #

### [http://www.slideshare.net/adamhjk/infrastructure-automation-with-chef](http://www.slideshare.net/adamhjk/infrastructure-automation-with-chef) ###

!SLIDE

# Why do we use Chef?#

!SLIDE

## If we do it manually, we're bound to forget something ##

!SLIDE

# Mostly happy puppet users #
### Though don't get me started on stored configs ###

!SLIDE

# Met Adam at Velocity #
# ![Velocity](velocity08.png) #

!SLIDE

# Learned about iclassify #
### Interesting ideas ###
### Painful implementation ###
### Never made it to production ###

!SLIDE

# Then along came chef #
### And a chance to build a new datacenter ###

!SLIDE bullets

# Some minor wins #
* Implicit ordering
* Ruby outside templates

!SLIDE bullets

# Some major wins #
* Search
* Source of truthiness
* knife

!SLIDE

# Some downsides #

!SLIDE smaller commandline
# Less clear error messages #
    /usr/lib/ruby/1.8/chef/mixin/template.rb:43:in `render_template': undefined method `[]' for nil:NilClass (Chef::Mixin::Template::TemplateError)
        from /usr/lib/ruby/1.8/chef/provider/template.rb:100:in `render_with_context'
        from /usr/lib/ruby/1.8/chef/provider/template.rb:39:in `action_create'
        from /usr/lib/ruby/1.8/chef/runner.rb:51:in `send'
        from /usr/lib/ruby/1.8/chef/runner.rb:51:in `run_action'
        from /usr/lib/ruby/1.8/chef/runner.rb:109:in `converge'
        from /usr/lib/ruby/1.8/chef/runner.rb:108:in `each'
        from /usr/lib/ruby/1.8/chef/runner.rb:108:in `converge'


!SLIDE code smaller

# Memory usage #
## Forced GC only goes so far ##
    @@@ sh
    #!/bin/bash
    # Will start within 30 min of being called
    sleep $(($RANDOM%1800))
    
    if [ ! -f "/etc/chef/disabled" ]; then
        nice chef-client -L /var/log/chef/client.log -l info
    fi

!SLIDE bullets
        
# Performance #
* Only uses one core
* http://timetobleed.com/fixing-threads-in-ruby-18-a-2-10x-performance-boost/

!SLIDE

# Interesting bits about our chef install

!SLIDE

# chef-server #

!SLIDE bullets

# Physical hardware #
* pxe
* preseed
* postinstall chef bootstrap

!SLIDE code

# Virtual Machines #
    @@@ javascript 
    "vms": { "dev-jason": { "cpus": "2",
                            "memory": "2048",
                            "distro": "karmic",
                            "ip": "10.1.1.111",
                            "disk": "15360" 
                          }
            }

!SLIDE code

# Nodes are datacenter aware #
    @@@ ruby
    role['SJC']
    role['Iowa']
    role['FRA']
    role['POZ']
    role['LON']

!SLIDE bullets

# Heavy users of search #
* /etc/hosts
* bind
* ganglia
* nagios

!SLIDE bullets

# Tool integration #
* Deploy scripts
* Racktables import

!SLIDE

# The missing bits about effective Chef#

!SLIDE

# null happens, code defensively #
          
!SLIDE

# Think about persistence #
### The route provider is run-time only ###

!SLIDE

# Be wary of searching on "\*:\*"
### Results are full node objects ###

!SLIDE

# Use some sort of exception mechanism #
### knife status only shows that a node object was updated ###

!SLIDE

# Is an attribute really what you want? #
### Think about visibility and versioning ##



