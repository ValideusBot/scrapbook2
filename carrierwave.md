# CarrierWave File Uploader Scrapbook



## testing with RSpec 

in All examples I'll be suspecting that I'm dealing with a model with mounted Uploader

```ruby
# app/models/document.rb
class User < ActiveRecord::Base
  mount_uploader Avatar
    
  # ...
end

```

and in our specs we have included this helpfull macro file

```ruby
# spec/support/upload_file_macros.rb

require 'carrierwave/test/matchers'
module UploadedFileMacros
  def self.included(base)
    base.send :include, CarrierWave::Test::Matchers
  end

  def uploaded_file
    ActionDispatch::Http::UploadedFile.new({
      :filename => 'blank_pdf.pdf',
      :content_type => 'pdf',
      :tempfile => file_for_upload
    })
  end

  # For comparing the upladed file match
  #
  #    document.file.file.file.should be_identical_to file_for_upload
  # 
  # or you can directly set record file:
  # 
  #    document.file = file_for_upload
  #
  def file_for_upload
    File.new("#{Rails.root}/spec/fixtures/uploads/blank_pdf.pdf")
  end
end
```

### Copy file from one resource to other 

check https://github.com/equivalent/copy_carrierwave_file


### Set and compare existance of file with Carrierwave uploader record specs

...or: How to mock/stub file in model that is using  CarrierWave uploader

```ruby
require 'spec_helper'
describe User do
  include UploadedFileMacros
  
  it do
    user = User.new(avatar: file_for_upload)
    user.avatar.file.exists?  #=>true
    user.avatar.file.file.should be_identical_to file_for_upload
  end
end
```


### Seting a record file in factory

Factory:

```ruby
# spec/factories/documents.rb
FactoryGirl.define do

  factory :document, class: Document do
    description "document"
    
    trait :with_file do
      after :create do |d|
        d.file.store!(File.open("#{Rails.root.to_s}/spec/factories/uploads/blank_pdf.pdf"))
      end
    end
    
  end
end
```

in spec file:

```ruby
# spec/models/document_spec.rb
require 'spec_helper'
desc Document do
  include UploadedFileMacros

  let(:document){ create :document, :with_file }
  it 'document should have file' do
    document.file.file.file.should be_identical_to file_for_upload
  end
end
```


relevant links:

* http://stackoverflow.com/questions/4511586/rails-functional-test-case-and-uploading-files-to-actiondispatchhttpuploadfi


rails: 3.2.14

published: 16.09.2013


### Tell my specs/tests to use different root path

I don't want my test to store files to `~/projects/my_project/public/uploads` folder but
rather `~/projects/my_project/tmp/uploads` folder


```ruby
# app/uploaders/avatara_uploader.rb
class AvatarUploader < CarrierWave::Uploader::Base
  storage :file
  
  # ...
  def root
    Rails.env.test? ? "#{Rails.root.to_s}/tmp/" : super
  end
  # ...
end
```

all other settings (like `store_dir`) will stay unchanged 

rails: 3.2.14

published: 16.09.2013



# Issues 

### RMagick complaining about libMagickCore.5.dylib not found in OSX

1/ search for this lib in the system 

    sudo find / -name "libMagickCore.5.dylib" -print

I found mine in `usr/local/Cellar/imagemagick/6.7.7-6/lib/libMagickCore.5.dylib`

2/ link this library to required path 

    ln /usr/local/Cellar/imagemagick/6.7.7-6/lib/libMagickCore.5.dylib /usr/local/lib/libMagickCore.5.dylib
    
http://stackoverflow.com/questions/19040932/rmagick-complaining-about-libmagickcore-5-dylib-not-found-in-osx/19040933#19040933
