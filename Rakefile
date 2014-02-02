require "execjs"

desc "Run the app's server in either development or production mode"
task :server do
  environment = 'development'

  if ARGV.last.match(/(development|production)/)
    environment = ARGV.last
  end

  Rake::Task["assets:precompile"].invoke

  puts "Starting SassMeister in #{environment.upcase} mode..."

  system("bundle exec rackup config.ru -p 3000 -E #{environment}")

  task environment.to_sym do ; end
end


# Heroku will run this task as part of the deployment process.
desc "Compile the app's Sass"
task "assets:precompile" do
  coffee = File.read("lib/coffee-script.js")
  source = File.read('coffee/embed.coffee')

  context = ExecJS.compile(coffee)
  js = context.call("CoffeeScript.compile", source)

  File.open("public/javascripts/embed.js", 'w') {|f| f.write(js) }

  system("bundle exec jammit --force")
  system("bundle exec compass compile -e production")

  File.open("config/build.txt", 'w') {|f| f.write(Time.now.to_i) }
end
