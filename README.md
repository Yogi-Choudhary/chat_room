# README

This README would normally document whatever steps are necessary to get the
application up and running.

Things you may want to cover:

* Ruby version

* System dependencies

* Configuration

* Database creation

* Database initialization

* How to run the test suite

* Services (job queues, cache servers, search engines, etc.)

* Deployment instructions

* ...

* add devise gem

configration for turbo with devise for this step 

create a new file **app/controllers/turbo_devise_controller.rb**

* add this code in this file **turbo_devise_controller.rb**

```
class TurboDeviseController < ApplicationController
  class Responder < ActionController::Responder
    def to_turbo_stream
      controller.render(options.merge(formats: :html))
    rescue ActionView::MissingTemplate => error
      if get?
        raise error
      elsif has_errors? && default_action
        render rendering_options.merge(formats: :html, status: :unprocessable_entity)
      else
        redirect_to navigation_location
      end
    end
  end

  self.responder = Responder
  respond_to :html, :turbo_stream
end
```

* add this code in **devise.rb** file

```
Devise.setup do |config|
  # ...
  
  # ==> Controller configuration
  # Configure the parent class to the devise controllers.
  config.parent_controller = 'TurboDeviseController'
  
  # ...

  # ==> Navigation configuration
  # ...
  config.navigational_formats = ['*/*', :html, :turbo_stream]

  # ...

  # ==> Warden configuration
  # ...
  config.warden do |manager|
    manager.failure_app = TurboFailureApp
  #   manager.intercept_401 = false
  #   manager.default_strategies(scope: :user).unshift :some_external_strategy
  end
end  
```

**generate a new controller**

rails g controller pages home

add this code in **home.html.erb** file
```
<%if current_user%>
	<%= button_to "Logout", destroy_user_session_path, method: :delete %>
<%else%>
	<%= link_to "Login", new_user_session_path %>
	<%= link_to "Sign Up", new_user_registration_path%>
<%end%>
```

add this code in **routes.rb** file

```
root 'pages#home'
  devise_for :users

  devise_scope :user do
    # Redirests signing out users back to sign-in
    get "users", to: "devise/sessions#new"
  end
```