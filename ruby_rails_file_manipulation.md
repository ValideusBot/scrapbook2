# pure Ruby

### filename

```ruby
pathname = Pathname.new('foo/somefile.rb') 
# => #<Pathname:...>

pathname.to_s
# => 'foo/somefile.rb

pathname.basename.to_s
# => 'somefile.rb'

# get filename without extension
pathname.basename(".*").to_s
# => 'somefile'
```

this can be used to load child classes in same dir:

```ruby

# lib/application_strategy/foo.rb
module ApplicationStrategy
  class Foo
  end
end

# lib/application_strategy/bar_car.rb
module ApplicationStrategy
  class BarCar
  end
end

# lib/application_strategy/base.rb
module ApplicationStrategy
  class Base
  
    def self.strategies
      @strategies ||= begin
        Dir.glob("#{File.dirname(__FILE__)}/*")
          .collect { |file_path| Pathname.new(file_path).basename(".*").to_s }
          .select { |name| name != 'base' }
          .collect{ |name| "ApplicationStrategy::#{name.classify}".constantize } # classify & constantize are Rails methods
      end
    end 
    
  end
end

ApplicationStrategy::Base.strategies 
# => [ApplicationStrategy::Foo, ApplicationStrategy::BarCar]
```


### current folder

```ruby
File.dirname(__FILE__)
#=> "."
```

### require files releted to current folder

```ruby
# test/test_helper.rb
Dir[File.dirname(__FILE__)+"/support/**/*.rb"].each {|f| p  require f}   
#  => ["./test/support/upload_file_macros.rb"] 
```

### Remove all files inside directory

```ruby
require 'fileutils'
FileUtils.rm_rf 'foldername'
FileUtils.rm_rf(Dir.glob("./tmp/uploads/*"))
```
    
### Create dir 

~~~ruby
require 'fileutils' 
FileUtils.mkdir '/media/foldername'

Dir.mkdir "/media/myfolder", 0700
`sudo mkdir /media/myfolder`
~~~

### Directory exist ?

~~~ruby
File.directory?('path/to/something')
~~~

### Directory empty ?

```ruby
Dir["/media/myfolder/*"].empty?
```
    
### Rename files in path

    Dir.glob(folder_path + "/*").sort.each do |f|
      File.rename(f, folder_path + "/" + filename.capitalize + File.extname(f))
    end
    
This solution will keep the extension the way they were


### reading and writing to from file

~~~ruby

content =  normal_users.last.to_json

json_file =  File.open(target, "w") do |f|
  f.write(content)
end
json_file.close

~~~

or

~~~ruby

target = "#{Rails.root.to_s}/tmp/old_database_migration.json" 

data = ''
f = File.open(target, "r") 
f.each_line do |line|
  data += line
end
f.close

p data
~~~


this will keep the `./tmp/uploads/` dir present, but will be empty

http://stackoverflow.com/questions/8538427/how-to-delete-all-contents-of-a-folder-with-ruby

# Rails

### root

```ruby
Rails.root.to_s
```

### print all models

~~~ruby
Dir["#{Rails.root}/app/models/**/*.rb"].each {|file| print file }
~~~

### load/reload all models and decorators

```ruby
Dir["#{Rails.root}/app/models/**/*.rb", "#{Rails.root}/app/decorators/**/*.rb"].each { |file| load file }
Dir["#{Rails.root}/app/models/**/*.rb"].each { |file| load file }

```


### require specific file

```ruby
require Rails.root.join('lib', 'tld_constraint')
# equivalent of : require '/home/usr/my_rails_app/lib/tld_constraint'
```
     
     
### require all files in folder

```ruby
Dir[Rails.root.to_s + '/app/services/**/*.rb'].each {|file| require file }

# watch out, if it's for config, you can do in far simpler

module MyApp
  class Application < Rails::Application
    config.autoload_paths += %W(#{Rails.root}/app/models/concerns)
  end
end
```
     
