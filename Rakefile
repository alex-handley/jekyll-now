require "rubygems"
require "bundler/setup"
require "jekyll"

namespace :site do
	desc "Generate blog files"
	task :generate do
		Jekyll::Site.new(Jekyll.configuration({
			"source"      => ".",
			"destination" => "_site"
		})).process
	end

  desc "Generate and publish blog"
  task publish: [:generate] do
  	cp_r "_site/.", "../"

		system "cd .."
		system "git add ."
		message = "Site updated at #{Time.now.utc}"
		system "git commit -m #{message.inspect}"
		# system "git push origin master:refs/heads/gh-pages --force"

  end
end

