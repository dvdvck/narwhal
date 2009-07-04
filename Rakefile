#!/usr/bin/env ruby

# FIXME: make this a Narwhal program!

require "fileutils"

title_map = {
  "index" => "introduction"
}

DEFAULT_LAYOUT_TEMPLATE = '_layouts/default-template.html'
DEFAULT_LAYOUT = '_layouts/default.html'

task :default => [:build]

task :all => [:checkout, :build, :runserver]

task :checkout do
  puts "Checking out 'doc' from master"
  `git checkout master doc`
end

task :runserver do
  `jekyll --auto --server`
end

task :build do
  puts "Building"

  articles = ""

  # find all Markdown files and copy them over, prepending the YAML header
  docs = Dir.glob "doc/**/*.md"
  docs.each do |doc|
    partial_path = doc.match(/doc\/([^.]+)\.md/)[1]
    output_path = "#{partial_path}.md"
    title = partial_path.gsub(/-/, ' ').gsub(/\//, ' - ')
    
    title = title_map[title] || title
  
    articles += "- name: #{title.gsub(/-/,'/')}\n  link: \"/#{partial_path}.html\"\n"
  
    mkdir_p File.dirname(output_path), :verbose => false
    text = File.read(doc)
    File.open(output_path, 'w') do |f|
      f.write("---\nlayout: default\ntitle: narwhal - #{title}\n---")
      f.write(text)
    end
  end

  layout = File.read(DEFAULT_LAYOUT_TEMPLATE)
  header = <<EOS
---
github_url: "http://github.com/tlrobinson/narwhal"
articles:
#{articles}
links:
- name: jack & jsgi
  link: "http://jackjs.org/"
---
EOS

  File.open(DEFAULT_LAYOUT, 'w') do |f|
    f.write(header)
    f.write(layout)
  end
  
end

task :clean do
  rm_rf "doc"
  rm_rf "_site"
  rm_f DEFAULT_LAYOUT
  Dir.glob("**/*.md").each do |file|
    rm_f file
    d = file
    until (d = File.dirname(d)) =~ /^(\.|)$/ do
      rmdir d
    end
  end
end