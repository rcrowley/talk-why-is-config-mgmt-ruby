!SLIDE bullets

# Why is configuration management software written in Ruby?

* <http://rcrowley.org/talks/ruby-on-ales-2011/>



!SLIDE bullets

# Hi, I&#8217;m Richard Crowley

* Equal opportunity technology hater.
* DevStructure&#8217;s operator and UNIX hacker.



!SLIDE bullets

# Puppet



!SLIDE bullets

# Puppet

* There once was a systems administrator named Luke who didn't want to use CFEngine anymore...



!SLIDE bullets

# Puppet

* Ruby won out over Python and Perl.
* The autoloader is the frequently cited reason.



!SLIDE bullets

# Puppet autoloader

* `glob` for source files.
* Each one registers itself using an internal DSL.



!SLIDE bullets

# Puppet internal DSL

	@@@ Ruby
	Puppet::Type.newtype(:package) do
	  # ...
	  def insync?
	    # ...
	  end
	  # ...
	end



!SLIDE bullets

# Internal DSLs

* Give one kind of context while taking another away.



!SLIDE bullets

# Internal DSLs

	@@@ Ruby
	Puppet::Type.newtype(:package) do
	  # ...
	end

* It's obvious this introduces a type.
* It's not obvious what Ruby code goes here.



!SLIDE bullets

# Internal DSLs

## These are questions I shouldn't have to ask:

* Can I define classes, modules, and/or methods in this scope?
* What is the fully-qualified name of a constant defined in this scope?
* Can I `return` in this scope?



!SLIDE bullets

# Puppet resources

	@@@ Puppet
	package { "ruby": ensure => installed }
	file { "/etc/sysctl.conf":
		ensure => file,
		group => "root",
		mode => 0644,
		owner => "root",
		source => "puppet:///sysctl.conf",
	}

* Orthogonal resources.
* Puppet makes its best-effort.



!SLIDE bullets

# Puppet logging



!SLIDE bullets

# WTF?

	puppet agent --verbose



!SLIDE bullets

# Seriously, WTF?

	puppet agent --verbose --debug --trace



!SLIDE bullets

# Puppet logging

* Uses syslog-appropriate levels.
* Color-coded if logging to a TTY.



!SLIDE bullets

# Chef



!SLIDE bullets

# Chef versus Puppet

## This is not a religious war

* Functionally similar.
* Philisophically different.



!SLIDE bullets

# Chef

* External Ruby DSL.
* Internal Ruby Ruby.



!SLIDE bullets

# Chef resources

	@@@ Ruby
	package "ruby" do
	  action :install
	end
	cookbook_file "/etc/sysctl.conf" do
	  group "root"
	  mode 0644
	  owner "root"
	  source "sysctl.conf"
	end



!SLIDE bullets

# Chef insides

	@@@ Ruby
	require 'chef/resource'

	class Chef
	  class Resource
	    class Package < Chef::Resource
	      # ...
	    end
	  end
	end



!SLIDE bullets

# External DSLs

## Homegrown grammar versus piggybacking Ruby

* Not better.  Not worse.



!SLIDE bullets

# Internal DSLs

## "What scope is that?" versus Ruby Ruby

* This is not the place to get fancy.



!SLIDE bullets

# Whatever, I still like Puppet better

* Let's drink beer and argue about it, though.



!SLIDE bullets

# Despite their differences, Puppet and Chef both codify common systems administration tasks

* That sounds like how people used to use Perl.



!SLIDE bullets

# Ruby as a better Perl

* First-class regular expressions like Perl.
* Backtick operator like Perl and shell.
* Relaxed parentheses like Perl.



!SLIDE bullets

# Ruby as a _better_ Perl

* Blocks are better than subroutine references.
* Real objects are better than fake objects.  No more structs full of function pointers!



!SLIDE bullets

# RubyGems == CPAN

* No release management or feature freezes.
* Double-edged sword.



!SLIDE bullets

# RubyGems for people on-call

* Regression testing.
* Semantic versioning.
* Slow-and-steady release cycle.



!SLIDE bullets

# RubyGems for people on-call

* Stability first.
* No one wants to depend on a moving target.



!SLIDE bullets

# Rake

* A better GNU `make`.
* External Ruby DSL.  Internal Ruby Ruby.



!SLIDE bullets

# Rake

	@@@ Ruby
	task :deploy do
	  sh "git push production master"
	end
	file "foo.o" => "foo.c" do |t|
	  sh "gcc -o foo.o foo.c"
	end



!SLIDE bullets

# Dependency programming

* Start with a goal.
* Satisfy its prerequisites first, recursively.
* If a prerequisite cannot be satisfied, abort.



!SLIDE bullets

# Rake

* Intended for interactive use.
* Logging isn't _as_ necessary with fail-fast execution.



!SLIDE bullets

# Capistrano

## `ssh`-in-a-`for`-loop meets Rake

* Dependency-oriented but works on multiple hosts in parallel.



!SLIDE bullets

# Capistrano

	$ cap foo
	  * executing `foo'
	  * executing "echo foo >&1"
	    servers: ["rcrowley.org"]
	    [root@rcrowley.org] executing command
	 ** [out :: root@rcrowley.org] foo
	    command finished
	  * executing "echo foo >&2"
	    servers: ["rcrowley.org"]
	    [root@rcrowley.org] executing command
	*** [err :: root@rcrowley.org] foo
	    command finished
	$

* Each host can fail or succeed independently.
* Everything's logged.



!SLIDE bullets

# The Marionette Collective

* Orchestration via STOMP publish/subscribe rather than SSH.
* Scalable upgrade to `ssh`-in-a-`for`-loop.



!SLIDE bullets

# The Marionette Collective

* General systems administration gone framework.



!SLIDE bullets

# The Marionette Collective

* Lots of small tools: `mc-ping`, `mc-find-hosts`, `mc-rpc`, and more.
* Compose these to form new programs.  _It's a UNIX system.  I know this._



!SLIDE bullets

# From middleware to MCollective

* Everything starts life as a "script."



!SLIDE bullets

# "Scripts?"

* So trivial you don't need to test them.



!SLIDE bullets

# From script to package

* Idempotence.
* `PUT` not `POST`.
* Manage whole files.



!SLIDE bullets

# API design

* Principle of least surprise.
* Namespaces.
* Look for generic solutions.  Not too hard, though.



!SLIDE bullets

# Why even bother with Ruby?



!SLIDE bullets

# Data structures programs versus UNIX programs



!SLIDE bullets

# UNIX programs

* Filesystem manipulation.
* System calls via GNU `coreutils`.



!SLIDE bullets

# Data structures programs

* Need more than POSIX shell's scalar variables.
* Need more than Bash's one-dimensional indexed and associative arrays.
* Classes, methods, sockets, threads, events, etc.



!SLIDE bullets

# Ruby can do both!



!SLIDE bullets

# UNIX programs in Ruby

* `File`, `Dir`, and `FileUtils`
* `Process`
* `Etc`
* Ruby editorializes too much.
* <http://tomayko.com/writings/unicorn-is-unix>



!SLIDE bullets

# Data structures in Ruby

* A hopefully obvious exercise left to the listener.



!SLIDE bullets

# Composable Ruby, too

* It's still a UNIX system.



!SLIDE bullets

# Declare inter-file dependencies

* Make it easy to use part of your package.



!SLIDE bullets smaller

# Don't make me try this hard...

	@@@ Ruby
	# Devise doesn't manage its own dependencies and the main
	# devise.rb is impossibly huge.
	require 'active_support/multibyte/chars'
	require 'restricted_login_validator'
	module ::Devise
	  ALL = [:database_authenticatable]
	  mattr_accessor :encryptor
	  @@encryptor = :sha1
	  mattr_accessor :pepper
	  @@pepper = "OH HAI"
	  mattr_accessor :stretches
	  @@stretches = 10
	  def self.friendly_token
	    ::ActiveSupport::SecureRandom.base64(15).tr("+/=", "-_ ").
	      strip.delete("\n")
	  end
	end
	require 'active_support/concern' #
	require 'warden'                 # Must be before devise/models.
	require 'devise/models'                          #
	require 'devise/models/database_authenticatable' # Must be before
	require 'devise/schema'                          # devise/orm/active_record.
	require 'devise/orm/active_record'
	require 'devise/encryptors/base'
	require 'devise/encryptors/sha1'



!SLIDE bullets

# ...just to do this

	@@@ Ruby
	require 'devise/orm/active_record'



!SLIDE bullets

# Explicit is better than implicit

* (Says _The Zen of Python_.)



!SLIDE bullets

# Coupling

## From the perspective of `foo.rb`

* Don't trust that your dependencies are met.
* This _implicitly_ couples you to the "loader."



!SLIDE bullets

# Magic

* The enemy of operable.
* Don't get cute: it's OK if your code looks like code.



!SLIDE bullets

# Patterns, anti-patterns, and philosophies



!SLIDE bullets

## Pattern

# External DSLs

* Brevity is a virtue.
* Require developer restraint.



!SLIDE bullets

## Anti-pattern

## Internal DSLs

* Obscure the Ruby Ruby scope.



!SLIDE bullets

## Pattern

# Dependency programming

* Powerful failure handling.
* Natural for interactive tasks.



!SLIDE bullets

## Pattern

# Idempotence

* Never be scared to run it again.



!SLIDE bullets

## Anti-pattern

# Magic

* Requires intimate knowledge to maintain and operate.



!SLIDE bullets

## Philosophy

# Explicit is better than implicit

* Reduces coupling.
* Provides context.
* Promotes reuse.



!SLIDE bullets

## Philosophy

# Look for generic solutions

* Not too hard, though.
* Strive to remove special cases.



!SLIDE bullets

# Thank you

* <richard@devstructure.com> or [@rcrowley](http://twitter.com/rcrowley)
* P.S. Use DevStructure!
