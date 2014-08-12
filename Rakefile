namespace :deploy do
	task :staging do
		`jekyll build`
		`rsync -avz --delete _site/ deployer@192.168.10.12:/var/apps/www.vincecima.com`
	end

	task :production do
	end
end
