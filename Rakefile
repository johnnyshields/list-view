require "bundler/setup"
require "ember-dev/tasks"
require "fileutils"

directory "tmp"

desc 'Copies version number from VERSION file into files which reference it'
task :copy_version do
  version = File.read('VERSION').strip!
  %w(bower.json docs/package.json docs/yuidoc.json).each do |file_name|
    replace = File.read(file_name).gsub!(/"version": ".+"/, %Q("version": "#{version}"))
    File.open(file_name, "w"){ |file| file.puts replace }
  end
end

desc 'Copies files from /dist to /components which are used by Bower'
task :copy_components do
  FileUtils.mkdir_p('components')
  %w(list-view.js list-view.min.js).each do |file_name|
    FileUtils.cp_r("dist/#{file_name}", "components/#{file_name}")
  end
end

desc 'Publishes build to CDN'
task :publish_build do
  root = File.dirname(__FILE__) + '/dist/'
  EmberDev::Publish.to_s3({
    :access_key_id => ENV['S3_ACCESS_KEY_ID'],
    :secret_access_key => ENV['S3_SECRET_ACCESS_KEY'],
    :bucket_name => ENV['S3_BUCKET_NAME'],
    :subdirectory => 'list-view',
    :files => ['list-view.js'].map { |f| root + f }
  })
end

task :clean => "ember:clean"
task :dist => "ember:dist"
task :test, [:suite] => "ember:test"
task :default => [:dist, :test]
task :release => [:dist, :test, :copy_version, :copy_components, :publish_build]
