# MultiTenant

MultiTenant is a Rails 3 plugin to help ease the development of web applications
that utilise the database in a multi-tenant manner, and provide each end-user/account
with their own subdomained version of the application.

This plugin is currently work in progess. Managing subdomains still to do..

# Installation
Currently just a regular plugin, typical plugin installation:
    rails plugin install git://github.com/mconnell/multi_tenant.git

Alternatively as a git submodule:
    git submodule add git://github.com/mconnell/multi_tenant.git vendor/plugins/multi_tenant

# How to Use

## Models
Add `multi_tenant_model` to the primary model
    class Account < ActiveRecord::Base
      multi_tenant_model
      has_many :properties
    end

Any models that should be explicitly scoped to a `multi_tenant_model` require the `belongs_to_tenant` to be set
    
    class Property < ActiveRecord::Base
      belongs_to_tenant :account
    end

Property scoping will behave as normal unless a current account has been set:
    
    Property.all
    #> [#<Property account_id: 1>, #<Property account_id: 2>]
    Account.current = Account.find(1)
    Property.all
    #> [#<Property account_id: 1>]

If the current account is set, instantiating new property records will automatically be assigned to the current account:
    Property.new
    #> <Property account_id: nil>
    
    Account.current = Account.find(25)
    Property.new
    #> <Property account_id: 25>

## Controller
If you want to have subdomained instances of the application:
    class ApplicationController < ActionController::Base
      subdomain_by_multi_tenant_model :account
    end

This basically tells the app to set the current account to Account.find_by_subdomain!(subdomain). Will Throw an exception if account can't be set. Every subdomain will also have an independent session.

The controller aspect is currently not tested in isolating in the plugin as I haven't gotten around to working out how to generate routes in rails 3 for test controllers. They'll be extracted out of the application I'm using this plugin for eventually but you'll probably want to write some in your application..


Copyright (c) 2010 [Mark Connell], released under the MIT license
