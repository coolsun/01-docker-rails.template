https://docs.docker.com/compose/rails/#define-the-project

# N can be any number from 2 - 9
cp -r 01-docker-rails.template 0N-docker-rails


# in docker-compose.yml
change 3416 to 34N6
change 3010 to 30N0

# files
.vscode/launch.json
Dockerfile
docker-compose.yml
Gemfile
Gemfile.lock

# build
# IMPORTANT: when Dockfile or Gemfile is changed, need to build again!!!!!!
docker-compose build

# rails new
docker-compose run web3010 rails new . --force --database=mysql


#config/database.yml => add password for mysql and change host for docker
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password: password
  host: mysql5.6-3416



# IMPORTANT: need to comment out bootsnap for debugger to stop in breakpoints!!!!!
# commented out bootsnap in config/boot.rb
# bsun 2018/11/21 avoid not stopping in break point issue
#require 'bootsnap/setup' # Speed up boot time by caching expensive operations.

# commented out bootsnap in Gemfile
# bsun 2018/11/21 avoid not stopping in break point issue
#gem 'bootsnap', '>= 1.1.0', require: false

#comment out tzinfo gem at Gemfile
# gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]

# add ruby-rdebug-ide and debase gem at the bottom of Gemfile
gem 'ruby-debug-ide'
gem 'debase'

# create db - change N to the right digit
docker-compose run web3010 rake db:migrate

# Since Gemfile is changed, need to build again
docker-compose build

# create user - change N to the right digit
docker-compose run web3010 rails generate scaffold User name:string email:string

# inspect web3010
docker-compose run web3010 bash

# debug with Visual Studio Code
1. cd 0N-docker-rails
2. start the docker servers with the command
   $ docker-compose down;echo rm tmp/pids/server.pid;sleep 3;rm tmp/pids/server.pid;docker-compose up

   # you can add alias to ~/.bash_profile
   $ alias dsd='docker-compose down;echo rm tmp/pids/server.pid;sleep 3;rm tmp/pids/server.pid;docker-compose up'

2. With visual studio code, open the folder 0N-docker-rails
3. Click on the Debug icon on the left menu
4. Run "Attach to Docker
5. Open app/controllers/user_controller.rb
   set break point on index function

   def index
    @users = User.all
6. Open browser and hit http://localhost:30N0/users

# Known bugs:
1. when stop the debugger, the web3010 will exit.  
   It seems there is no way to detach web3010 process from Visual Studio Code
