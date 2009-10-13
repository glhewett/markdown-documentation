require 'rubygems'
require 'rdiscount'                                  
require 'fileutils'
require 'erb'              
require 'hpricot'
require 'lib/outline.rb'                

task :default => :build
markdown_docs = Dir["*.markdown"]

markdown_docs.each do |document|
  namespace :build do
    desc "build the html for #{document}"
    task document.to_sym do |t|
      if File.writable?(document.gsub(/\.markdown$/, '.html')) or not File.exists?(document.gsub(/\.markdown$/, '.html'))
        puts "building #{document}"
        output = File.open(document.gsub(/\.markdown$/, '.html'), 'w')
        input = File.open(document, 'r')
        @document_name = document           
        @document_content = RDiscount.new(input.read).to_html 
        layout = ERB.new(File.read("layout.html.erb")).result
        output.write(Webby::Filters::Outline.new(layout).filter)
      else
        puts "skipped #{document}. not writable."
      end
    end                
  end                     
  
  namespace :clean do
    desc "clean the html for #{document}"
    task document.to_sym do |t|
      puts "removing #{document}"
      rm document.gsub(/\.markdown$/, '.html')
    end
  end
end

desc "build all markdown documents"
task :build => markdown_docs.collect { |f| "build:#{f}" }

desc "clean all markdown documents"
task :clean => markdown_docs.collect { |f| "clean:#{f}" } 

desc 'Publish Website'
task :publish do
  host = "serveradmin%dibfruitcake.com@s70095.gridserver.com"
  remote_dir = "/home/70095/users/.home/domains/greg.dibhost.com/html/"
  sh "rsync --delete -rulptzCF . #{host}:#{remote_dir}"
end
