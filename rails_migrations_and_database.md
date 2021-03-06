* MySQL notes https://github.com/equivalent/scrapbook2/blob/master/mysql.md
* PostgreSQL notes https://github.com/equivalent/scrapbook2/blob/master/postgresql.md

# database configuration

```ruby
# config/database.yml

default: &default
  adapter: mysql2
  encoding: utf8
  reconnect: false
  pool: 5
  username: root
  password: imot
  #socket: /var/run/mysqld/mysqld.sock

development:
  <<: *default
  database: my_app_development

test:
  <<: *default
  database: my_app_test<%= ENV['TEST_ENV_NUMBER'] %>

  
```

# Rails migrations

### Add and remove index

```ruby
class AddDocumentNameOwnershipIndex < ActiveRecord::Migration
  def up
    add_index :document_name_ownerships, 
      [:owner_type, :owner_id],
      name: :dn_ownerships_ix, 
      uniq: true
  end

  def down
    remove_index :document_name_ownerships, name: :dn_ownerships_ix
  end
end
```

Note: multiple inxexes are read from left to right

* http://dev.mysql.com/doc/refman/5.0/en/multiple-column-indexes.html
* http://stackoverflow.com/questions/13298545/how-to-specify-a-multiple-column-index-correctly-in-rails



# Other Rails db tricks

### Random record in mysql

~~~ruby
Person.find(:first, :order => 'rand()')
Model.first(:order => "RANDOM()") 
Thing.order("RANDOM()").first
Thing.offset(rand(Thing.count)).first
Recommendation.offset(rand(Recommendation.count)).where('person_id != 1').first
~~~

### Trigger direct mysql command from rails

~~~ruby
ActiveRecord::Base.connection.execute("TRUNCATE #{ApplicationBuildCommand.table_name}")  
~~~

### Explain queries for Arel in Rails console

```ruby
Document.where(id: 1).explain
# => EXPLAIN SELECT `documents`.* FROM `documents` WHERE `documents`.`id` = 1 AND (`documents`.`deleted_at` IS NULL)
```
