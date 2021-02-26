---
layout: post
title:      "coding a city apartment finder app."
date:       2021-02-25 20:35:25 -0500
permalink:  coding_a_city_apartment_finder_app
---

Starting the project was the most difficult part for me. I had no idea where to begin or what to code. I was able to decide on my application idea when I looked at the tabs I had opened on my laptop. I have been searching for an apartment in NYC to move in the future. As I was looking, I already saw models and attributes pop up in my head as they were right there. My models would be amenity, apartment_amenities, apartment, building, owner, and a user! From here I was already mentally typing ```rails g model``` with my generators in the terminal. Once I got started on my models I added in their relationships to each other in their files. The Amenity ```has_many :apartment_amenities``` and ```has_many :apartments, through: :apartment_amenities```.  The ApartmentAmenity ```belongs_to :apartment``` and it ```belongs_to :amenity```. My Apartment would ```belongs_to :building```, ```has_many :apartment_amenities```, and ```has_many :amenities, through: :apartment_amenities```. Just like these examples, I continued to fill out the model files like so. I also created my controllers in correspondence to my model names. 

There were many times that I had to remove columns, drop a table, and add columns to my tables. To remove columns I used ```rails g migration remove_column_name_from_table_name column_name:datatype --no-test-framework```. To add a column I used ```rails g migration add_column_name_to_table_name column_name:datatype --no-test-framework```. Lastly, to drop a table I used ```rails g migration drop_table :table_name```. Don’t forget to run ```rails db:migrate``` after your changes! Another useful tip is to have another terminal tab open and running ```rails s``` to just have your localhost:3000 running so you can check how your code is working. 

For my views, depending on which views I was working on for example, views/buildings/_form.html.erb or views/owners/_form.html.erb I used partials. Partials are my view level files that only form a part of my html page. By using a partial I can remove repeated html. That underscore is to indicate that these files are a partial. I used ```form_for``` in my forms. form-for takes in an object so I’d pass in for ex @shoe (you would have to set this in your new action). I used tags and called them on my form builder for ex, f.label. Don’t forget the submit_tag! form_for will check to see that the object you passed in has been saved to the database yet. If it hasn’t been saved then the form knows this is a new form for a new.   

Back to the controllers, for my home_controller.rb I had it go to my homepage in views which would be the welcoming screen for the owners of properties in my application. In both my buildings and owners controllers, I have a show action. For my building show action I added ```@building = Building.find(params[:id])``` and ```@url = owner_building_path```. In my owners show action I added ```@owner = Owner.find(params[:id])``` and ```@building = @Owners.building.all```. The params[:id] is meant to be a string that identifies a restful resource within my application. It will identify a specific instance for a resource.  I added in my CRUD actions as well. In my BuildingsController, I added strong params. Strong params makes sure that when a user submits a form it only lets the fields you want get by. 
 
   ```private
 
   def building_params
       params.require(:building).permit(:name, :address, :owner_id)
   end```
	 
So it must have that building key and im permitting certain attributes. For the create action I used path helpers (i.e. ```redirect_to owner_building_path()). I also added a ```flash[:message] = “Could not be saved." if it was unable to save the new building. 

Your validations live in your models. One of the validations I used for example was in my Apartment model. I listed my attribute which is :unit and the validation lets you know that it is not valid without that attribute. ```validates :unit, presence: true```. 
Since I added validations for a web form, I want to display an error message when it fails in views. 

For nested routes, usually the thing that you have many of something else is what gets nested under. You can view your nested routes in localhost:3000/rails/info/routes. You’ll want to see your new routes that get created when you for example do,

 ```resources :amenities, only: [:new, :show, :create]
   resources :apartments, only: [:index, :new, :create]```


My user table included attributes such as username, email, password_digest, uid, and provider (which would be the site the uid comes from). I also added the following gems, ```gem ‘dotenv-rails’```, ```gem ‘omniauth’```, ```gem ‘omniauth-rails_csrf_protection’```, and ```gem ‘omniauth-google-oauth2’```. I would need to get credentials from Google which was the site that I chose so I used https://console.development.google.com/cloud-resource-manager. I created a new project and created credentials by adding a URI. In my gitignore file I added .env and you should see your env file gray out. In the .env file I added variables, ```GOOGLE_CLIENT_ID=``` (this will create a hash and a key in hash)  and ```GOOGLE_CLIENT_SECRET=```. env is just a hash of information and it only works because I added the gem. Now I need to add middleware because Google is going to process the request. I went ahead and added omniauth.rb to config/initializers. In my routes, I send that request to Google and would need the route that’s going to come back. That’s the same route I listed under Google as my redirect URI. This request will go to the SessionsController which is responsible for logging the user in and out (creating/destroying a session). I added ```get ‘/auth/google_oauth2/callback’, to: ‘sessions#omniauth’ ``` which I created an action for in the SessionsController. In my user model I added ```has_secure_password``` (which adds password and password confirmation attributes. It validates that a password is present) and 

   ```def self.create_from_omniauth(auth)
       User.find_or_create_by(uid: auth['uid'], provider: auth['provider']) do |u|
           u.username = auth['info']['name']
           u.email = auth['info']['email']
           u.password = SecureRandom.hex(20)
   end```

