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
```html
  <input type="hidden" name="authenticity_token" value="<%= form_authenticity_token %>">
```
9. Flesh out the `#create` method in app/controllers/users_controller.rb to save submitted form data to the database.
```ruby
  @user = User.new(username: params[:username], email: params[:email], password: params[:password])

  if @user.save
    redirect_to new_user_path
  else
    render :new
  end
```
10. Nest your three User fields inside a hash in the HTML form (in app/views/users/new.html.erb)
```erb
  <form action="/users" method="post" accept-charset="UTF-8">
    <label for="email">Email:</label>
    <input type="text" name="user[email]"><br>
    <label for="username">Username:</label>
    <input type="text" name="user[username]"><br>
    <label for="password">Password:</label>
    <input type="password" name="user[password]"><br>
    <input type="submit">
  </form>
```
11. Setup strong parameters using `permit` and `require` (in app/controllers/users_controller.rb
```ruby
  def create
    @user = User.new(user_params) # Uses the hash from step 10
    #[...]
  end
  private
  def user_params
    params.require(:user).permit(:username, :email, :password)
  end
```
12. Using `form_tag` is about the same amount of work as using `<form>`, though it does take care of the charset and authenticity token for you. Change app/views/users/new.html.erb to use `form_tag`:
```erb
  <%= form_tag("/users", method: "post") do %>
    <%= label_tag("email", "Email:") %><br>
    <%= text_field_tag("user[email]") %><br>
    <%= label_tag("username", "Username:") %><br>
    <%= text_field_tag("user[username]") %><br>
    <%= label_tag("password", "Password:") %><br>
    <%= password_field_tag("user[password]") %><br>
    <%= submit_tag("Submit") %>
  <% end %>
```
13. `form_for` makes use of model objects to build the form. Modify #new in /app/controllers/users_controller.rb to instantiate a blank User object and store it in an instance variable called @user.
```ruby
  def new
    @user = User.new
  end
```
14. Change app/views/users/new.html.erb to use `form_for` and the user from the controller, and use default placeholders for `email` and `username`:
```erb
  <%= form_for @user do |f| %>
    <%= f.label :email %><br>
    <%= f.text_field :email, placeholder: "example@example.com" %><br>
    <%= f.label :username %><br>
    <%= f.text_field :username, placeholder: "Your user name here" %><br>
    <%= f.label :password %><br>
    <%= f.password_field :password %><br>
    <%= f.submit "Submit" %>
  <% end %>
```
15. Update the routes and controller to handle editing an existing user. Change /config/routes.rb:
```ruby
  resources :users, only: [ :new, :create, :edit, :update ]
```
And add `#edit` and `#update` to /app/controllers/users_controller.rb
```ruby
  def edit
    @user = User.find(params[:id])
  end
  
  def update
    @user = User.find(params[:id])
    if @user.update(user_params)
      redirect_to edit_user_path
    else
      render :edit
    end
  end
```
16. Create an Edit view /app/views/users/edit.html.erb (just copy /app/views/new.html.erb).
17. Modify your form view to display a list of the error messages that are attached to your failed model object if you fail validations.
```erb
  <% if @user.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@user.errors.count, "error") %> prohibited this user from being saved:</h2>
      <ul>
      <% @user.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
      </ul>
    </div>
  <% end %>
```
