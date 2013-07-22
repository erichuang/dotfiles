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
    cp file, out_file
  end
end

def install_dot(file)
  file_name = File.basename(file)
  out_file = File.join(ENV['HOME'], ".#{file_name}")
  if File.symlink?(out_file)
    puts "Skipping #{out_file}"
  elsif File.file?(out_file)
    puts "Appending to #{out_file}"
    File.open(out_file, 'a') do |f|
      f.write(File.read(file))
    end
  else
    puts "Linking #{out_file}"
    ln_s File.expand_path("../#{file}", __FILE__), out_file
  end
end


task :config do
  @config = YAML.load_file(File.expand_path('../config.yml', __FILE__))
end

desc 'build'
task :build => [:config] do
  mkdir_p BUILD_DIR
  Dir["#{TEMPLATE_DIR}/*"].each { |f| build_dot(f) }
end

desc 'install'
task :install => [:build] do
  Dir["#{BUILD_DIR}/*"].each { |f| install_dot(f) }
end
