require 'date'
require 'yaml'
require 'stringex'

task :post do
  today = Date.today
  dir   = "_posts"
  title = ENV["title"]
  filename = [today.strftime("%Y-%m-%d-"), title.to_url, ".md"].join
  path = File.join(dir, filename)

  header = {
    "layout" => "post",
    "title" => title,
    "tags" => [],
    "published" => true
  }.to_yaml

  File.open(path, "w:UTF-8") do |file|
    file.write([header, "---", "\n\n"].join(""))
  end

  puts path
end
