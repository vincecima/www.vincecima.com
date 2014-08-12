namespace :deploy do
	task :build do
		`jekyll build`
	end

	task :staging => [:build] do
		sync('192.168.10.12')
	end

	task :production => [:build] do
	end

	def sync(server)
		command = "rsync -avz --delete _site/ deployer@#{server}:/var/apps/www.vincecima.com"
		exec command
	end
end
