!SLIDE

# ** CHEF ** #

!SLIDE center

# ![BORK](swedish_chef_bork-sleeper-cell.jpg) #

!SLIDE

# I don't think you need me to tell you what chef is #

### [http://www.slideshare.net/adamhjk/infrastructure-automation-with-chef](http://www.slideshare.net/adamhjk/infrastructure-automation-with-chef) ###

!SLIDE

# Why do we use Chef?#
## If we do it manually, we're bound to forget something ##

!SLIDE

# Mostly happy puppet users #
### Though stored configs frequently made me sad ###

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


!SLIDE 

## Iowa datacenter built ground up in chef ##
## SJC has been coverted on a per cluster/host basis ##

!SLIDE bullets

# What we like about chef #
* Implicit ordering
* Ruby outside templates

!SLIDE bullets

# What we really like #
* Search
* Source of truthiness
* knife

!SLIDE

# Things that drag on us #

!SLIDE smaller commandline
# Less than stunning error messages #
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
# Server Performance #
* Only uses one core in packaged config
* Opcode and 37signals cookbooks are much better
* [Distro packaged ruby can kill another 10-20%]( http://timetobleed.com/fixing-threads-in-ruby-18-a-2-10x-performance-boost/)


!SLIDE

# Interesting bits about our chef install

!SLIDE bullets

# chef-server #
* Clustered thin behind nginx+ssl

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

!SLIDE 

# Heavy users of search #

!SLIDE 

# Build /etc/hosts and bind records

    @@@ ruby
    hosts = search(:node, "*:*")
    if hosts.size > 0
      template "/etc/hosts" do
        source "hosts.erb"
        mode 0644
        owner "root"
        group "root"
        variables(:hosts => hosts)
      end
    end

!SLIDE code smaller
# ganglia #
    @@@ ruby
    datacenter = node[:datacenter]
    datasources = get_datasources(datacenter)
    if datasources.size > 0
      template "/etc/ganglia/gmetad.conf" do
        source "gmetad.conf.erb"
        variables( :datasources => datasources )
        notifies :restart, resources(:service => 'gmetad')
      end
    end

!SLIDE code smaller
# nagios #
    @@@ ruby
    pool_apaches = search(:node, "(hostname:ap-*+OR+aliases:ap-*)+AND+datacenter:#{colo}")
    pool_memcache = search(:node, "(hostname:memcache*+OR+aliases:memcache*)+AND+datacenter:#{colo}")
    pool_db = search(:node, "(hostname:db-*+OR+aliases:db-*)+AND+datacenter:#{colo}")

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

!SLIDE code

# Avoid the line idiom

    @@@ sh
    #!/bin/sh -e
    #
    # rc.local
    # By default this script does nothing.

    exit 0
    /usr/local/bin/really_important_script

