# Settings for Rails 3

[![Build Status](https://travis-ci.org/ledermann/rails-settings.png?branch=master)](https://travis-ci.org/ledermann/rails-settings)
[![Code Climate](https://codeclimate.com/github/ledermann/rails-settings.png)](https://codeclimate.com/github/ledermann/rails-settings)

Ruby gem to handle settings for ActiveRecord instances by storing them as serialized Hash in a separate database table. Namespaces and defaults included.


## Usage

### Define settings

```ruby
class User < ActiveRecord::Base
  has_settings do |s|
    s.key :dashboard, :defaults => { :theme => 'blue', :view => 'monthly', :filter => false }
    s.key :calendar,  :defaults => { :scope => 'company'}
  end
end
```

If no defaults are needed, a simplified syntax can be used:

```ruby
class User < ActiveRecord::Base
  has_settings :dashboard, :calendar
end
```

Every setting is handled by the class `RailsSettings::SettingObject`. You can use your own class, e.g. for validations:

```ruby
class Project < ActiveRecord::Base
  has_settings :info, :class_name => 'ProjectSettingObject'
end

class ProjectSettingObject < RailsSettings::SettingObject
  validate do
    unless self.owner_name.present? && self.owner_name.is_a?(String)
      errors.add(:base, "Owner name is missing")
    end
  end
end
```

### Set settings

```ruby
user = User.find(1)
user.settings(:dashboard).theme = 'black'
user.settings(:calendar).scope = 'all'
user.settings(:calendar).display = 'daily'
user.save! # saves new or changed settings, too
```

or

```ruby
user = User.find(1)
user.settings(:dashboard).update_attributes! :theme => 'black'
user.settings(:calendar).update_attributes! :scope => 'all', :display => 'dialy'
```


### Get settings

```ruby
user = User.find(1)
user.settings(:dashboard).theme
# => 'black

user.settings(:dashboard).view
# => 'monthly'  (it's the default)

user.settings(:calendar).scope
# => 'all'
```


### Using scopes

```ruby
User.with_settings
# => all users having any setting

User.without_settings
# => all users without having any setting

User.with_settings_for(:calendar)
# => all users having a setting for 'calender'

User.without_settings_for(:calendar)
# => all users without having settings for 'calendar'
```


## Requirements

* Ruby 1.8.7, 1.9.3 or 2.0.0
* Rails 3.1 or greater (including Rails 4)


## Installation

Include the gem in your Gemfile und run `bundle` to install it:

```ruby
gem 'ledermann-rails-settings', :require => 'rails-settings'
```

Generate and run the migration:

```shell
rails g rails_settings:migration
rake db:migrate
```

## Compatibility

Version 2 is a complete rewrite and has a new DSL, so it's **not** compatible with Version 1. In addition, Rails 2.3 is not supported anymore. But the database schema is unchanged, so you can continue to use the data created by 1.x, no conversion is needed.

If you don't want to upgrade, you find the old version in the [1.x](https://github.com/ledermann/rails-settings/commits/1.x) branch. But don't expect any updates there.


## License

MIT License

Copyright (c) 2013 [Georg Ledermann](http://www.georg-ledermann.de)

This gem is a complete rewrite of [rails-settings](https://github.com/Squeegy/rails-settings) by [Alex Wayne](https://github.com/Squeegy)
