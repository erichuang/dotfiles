require 'rake'
require 'yaml'
require 'erb'


TEMPLATE_DIR = 'templates'
BUILD_DIR = 'build'


def build_dot(file)
  file_name = File.basename(file)
  if file_name =~ /.erb$/
    out_file = File.expand_path("../#{BUILD_DIR}/#{file_name.sub(/\.erb$/, '')}", __FILE__)
    File.open(out_file, 'w') do |f|
      f.write(ERB.new(File.read(file)).result(binding))
    end
  else
    out_file = File.expand_path("../#{BUILD_DIR}/#{file_name}", __FILE__)
    system %Q{cp #{file} #{out_file}}
  end
end

def install_dot(file)
  file_name = File.basename(file)
  out_file = File.join(ENV['HOME'], ".#{file_name}")
  if File.exists?(out_file)
    if File.lstat(out_file).symlink?
      puts "Skipping #{out_file}"
    else
      puts "Appending to #{out_file}"
      File.open(out_file, 'a') do |f|
        f.write(File.read(file))
      end
    end
  else
    puts "Linking #{out_file}"
    system %Q{ln -s "$PWD/#{file}" "#{out_file}"}
  end
end


task :config do
  @config = YAML.load_file(File.expand_path('../config.yml', __FILE__))
end

task :dots do
  system %Q{mkdir #{BUILD_DIR} -p}
  Dir["#{TEMPLATE_DIR}/*"].each { |f| build_dot(f) }
  Dir["#{BUILD_DIR}/*"].each { |f| install_dot(f) }
end

desc "install the dot files into user's home directory"
task :install => [:config, :dots]
