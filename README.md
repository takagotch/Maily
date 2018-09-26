### Maily
---

https://github.com/markets/maily

```sh
gem 'mainly'
rails g maily:install

bundle exec appraisal rake
```

```ruby
Maily.ebabled = ENV['MAILY_ENABLED']
Maily.enabled = Raild.env.production? ? false : true

config/initializers/maily.rb
Maily.setup do |config|
  # config.enabled = rails.env.production? ? false : true
  # config.allow_edition = Rails.env.production? ? false : true
  # config.allow_delivery = Rails.env.production? ? false : true
  # config.available_locales = [:en, :es, :pt, :fr]
  # config.base_controller = '::AdminController'
  # config.books_path = 'lib/maily_hooks.rb'
  # config.http_authorization = { username: 'admin', password: 'secret' }
  # config.welcome_message = "Welcome to our email tsting platform. If you have any problem, please contact support team at support@ex.com"
end


lib/maily_hooks.rb
user = User.new(email: 'user@ex.com')
lazy_user = -> { User.with_comments.first }
comment = Struct.new(:body).new('Lorem ipsum')
service = FactoryGirl.create(:service)

Maily.hooks_for('Notifier') do |mailer|
  mailer.register_hook(:welcome, user, template_path: 'users')
  mailer.register_hook(:new_comment, lazy_user, comment)
end

Maily.hooks_for('PaymentNotifier') do |mailer|
  mailer.register_hook(:invoce, user, service)
end

Maily.hooks_for() do |mailer|
  mailer.register_hook(:a_random_email, template_path: 'notifications')
  mailer.register_hook(:another_email, template_name: 'email_base')
end

Maily.hooks_for('BookingNotifier') do |mailer|
  mailer.register_hook(:accepted, description: "This email is sent when a reservation has been accepted by the system.")
end

Maily.hooks_for('Notifier') do |mailer|
  mailer.hide_email(:sensible_email, :secret_email)
end


Maily.base_controller = '::SuperAdminController'

class SuperAdminController < ActionController::Base
  before_action :maily_authorized?
  
  private
  def maily_authorized?
    current_user.admin? || raise("You don't have access to this section!")
  end
end

Maily.http_authorization = { username: 'admin', password: 'secret' }

```

