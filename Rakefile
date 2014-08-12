namespace :deploy do
	task :build do
		`jekyll build`
	end

	task :staging => [:build] do
		`rsync -avz --delete _site/ deployer@192.168.10.12:/var/apps/www.vincecima.com`
	end

	task :production => [:build] do
	end
end
