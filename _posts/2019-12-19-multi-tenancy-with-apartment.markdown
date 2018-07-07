---
layout: post
title:      "Multi-Tenancy in Rails with Apartment"
date:       2018-7-1 11:00:00 -0500
permalink:  multi-tenancy-with-apartment
published: false
---

I'm working on a [Rails app for nonprofit management](https://codehannah.nyc/theodora) that features multi-tenancy. This means that a single instance of application will support multiple isolated users. Today, we'll discuss how to configure multi-tenancy using the Apartment library with sessions.

## What is Multi-tenancy?

Multi-tenancy is a type of design architecture that allows an application to run multiple clients on one system. So, you can have multiple customers (customer == tenant == nonprofit administrative organization) logging into the same software. Slack, Discord, and Trello are examples of apps that feature multi-tenancy. Here are some primary characteristics of multi-tenant software:

* Data isolation for each tenant
* Customization for each tenant
* Common infrastructure across all customers


> Note: Multi-tenancy can be great, but sometimes you don’t need it. For example, if you are building social networking platform where the main idea is sharing data, you don’t need to separate data using multi-tenancy.


## Getting Started with Apartment

Apartment helps you add multi-tenancy by making it easy for you to sequester data based on a tenant. To use Apartment, you need to add it to your Gemfile, then run the install task with `bundle exec rails generate apartment:install`. Now, you're ready to get started using Apartment.

## Creating a Tenant

In order to select a tenant, we must first create a tenant. Since tenants map directly to Organizations in Theodora, it makes sense to have the tenant be created when the Organization is created. To do this, I simply used an `after_create` ActiveRecord callback which uses Apartment to create the tenant.

```ruby
class Organization < ApplicationRecord
  has_many :organization_users
  has_many :users, through: :organization_users

  after_create :create_tenant

  def tenant_name
    "organization_#{self.id}"
  end

  private

  def create_tenant
    Apartment::Tenant.create(self.tenant_name)
  end
end
```

## Configuring Apartment

When you install this gem, it creates `config/initializers/apartment.rb` file as the configuration file. There are a lot of knobs you can turn, but for our basic usage, we'll just need to touch the basics.

### Setting up Multi-tenant Models

In Theodora, each tenant has its own schema but shares the same database. Some multi-tenancy applications use separate DBs for each account or use scoped access by separating data by a unique id. I used Apartment and Postgres to isolate my data using the schema separation approach. Using schemas for separation provides strong data isolation and allows for running migrations separately for each tenant, which means there is less data to migrate at one time.

The models created are Organization, User, and OrganizationUser, and they are all in the global schema ("public" in Postgres). These models are global because they're all concerned with tenancy (the tenant name is derived from the Organization name), or are concerned with who has access to a tenant. Once I've got multi-tenancy support complete, I'll be able to flesh out the rest of the application, which will be isolated to the selected tenant.

>Example: A single grant manager may work at nonprofit known as an umbrella organization, where multiple nonprofits with overlapping missions will operate as one organization but may receive and report on separate grants. For example, one organization called "Center for Children" might have three local programs that run under three different statewide offices, such as Children's Advocacy Center (CAC), CASA (Court Appointed Special Advocate), Domestic Violence Hotline (DVH) all existing as one organization because their missions are all related to serving victims of abuse. The grant manager needs to be able to access all of the organizations seamlessly, without needing duplicate accounts.

You can see below that the models included in the global namespace are included below. You can also see how the configuration for schema separation is determined by `tenant_name`, which is derived from the Organization name. 

```ruby 
Apartment.configure do |config|
  config.excluded_models = %w{Organization User OrganizationUser}
  config.tenant_names = -> { Organization.all.map(&:tenant_name) }
end
```

```ruby
  def tenant_name
    "organization_#{self.id}"
  end
```

### Configuring Middleware

When using Apartment, you'll typically use an Elevator. In Apartment, Elevators assist in configuring the selected tenant on
a per-request basis. Elevators are Rack middlewares that run before the HTTP request hits your Rails application. It is very common
to use a subdomain multi-tenancy approach, which Apartment has built-in support for. However, since I'll be hosting this on a free
Heroku server, I didn't want to have to tangle with getting a custom wildcard SSL certificate to run the app securely. So, I needed to
customize how the tenant is selected, and so I used `Apartment::Elevators::Generic`, which allows you to choose your own strategy.

Since the selected tenant naturally is part of the user's session with the application, I decided to use the request session for tracking
which tenant they are using. Below, I check to ensure the user is logged in with Warden, and that a tenant has been selected. Then, I
look up the tenant (an Organization), scoped to the current user to ensure they have access to select the tenant. Finally, if an authorized
Organization is found, I return the tenant name to use to Apartment. For the rest of the request, any ActiveRecord calls will be isolated to
the tenant returned from this Elevator. Since I'm using Postgres, this means that all queries sent to the database will be limited to the tenant's Postgres schema, which is like a folder for tables.

```ruby
Rails.application.config.middleware.insert_after Warden::Manager, Apartment::Elevators::Generic, -> (request) { 
  if request.env['warden'].user && request.session[:selected_organization_id]
    org_id = request.session[:selected_organization_id]
    current_user = request.env['warden'].user

    if org_id.present?
      org = current_user.organizations.find_by(id: org_id)
      org.tenant_name if org
    end
  end
}
```

### Setting the Tenant

Once a user logs in, they are prompted to select which Organization they want to manage. This is used by the previously mentioned Elevator,
which sets the tenant (and Postgres schema) on each request. Here, I simply take the ID that was POSTed to the server, and set it in the
session key that the Elevator is expecting.

```ruby
class TenantSelectorController < ApplicationController
  def index
    @organizations = current_user.organizations
  end

  def create
    org = current_user.organizations.find(params[:id])
    session[:selected_organization_id] = org.id

    redirect_to root_path
  end
end
```

