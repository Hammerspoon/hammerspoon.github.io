source 'https://rubygems.org'

require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem "nokogiri", ">= 1.8.5"
gem "jekyll", ">= 3.7.4"
gem "rubyzip", ">= 1.2.2"
gem 'github-pages', versions['github-pages']

