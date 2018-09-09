# Re-Former

Part of the Odin Project [curriculum](https://www.theodinproject.com/courses/ruby-on-rails). The goal of this project is to create forms in Rails using HTML and the `form_tag` and `form_for` helpers.

Summary of steps:
1. Create the app.  
`$ rails new re-former`
2. Create a User model.  
`$ rails generate model User username:string email:string password:string`
3. Migrate the User model.  
`$ rails db:migrate`
4. Edit app/models/user.rb to add *presence* validations.
```ruby
  validates :username, presence: true
  validates :email, presence: true
  validates :password, presence: true
```
5. Create a `Users` controller with `#new` and `#create` actions. 
`$ rails generate controller Users new create`
6. Edit config/routes.rb to add Users resource for `:new` and `:create` actions.
```ruby
  resources :users, only: [:new, :create]
```
7. Create a `#new` view in file app/views/users/new.html.erb without using any form helpers.
```erb
<form action="/users" method="post" accept-charset="UTF-8">
	<label for="email">Email:</label>
	<input type="text" name="email"><br>
	<label for="username">Username:</label>
	<input type="text" name="username"><br>
	<label for="password">Password:</label>
	<input type="password" name="password"><br>
	<input type="submit">
</form>
```
8. Manually add the cross-site request forgery (CSRF) authenticity token hidden input to the form. Without it, the Rails server will issue an error message when the form is submitted.  
`<input type="hidden" name="authenticity_token" value="<%= form_authenticity_token %>">`
9. Flesh out the `#create` method in app/controllers/users_controller.rb to save submitted form data to the database.
```ruby
  @user = User.new(username: params[:username], email: params[:email], password: params[:password])

  if @user.save
    redirect_to new_user_path
  else
    render :new
  end
```
10. 
