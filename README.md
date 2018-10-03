### Swquel

https://github.com/jeremyevans/sequel

http://sequel.jeremyevans.net/documentation.html

roda
https://github.com/jeremyevans/roda

```ruby
require "sequel"
DB = Sequel.sqlite
DB.create_table :items do
  primary_key :id
  String :name, unique: true, null: false
  Float :price, null:false
end
items = DB[:items]
items.insert(name: 'abc', price: rand * 100)
items.insert(name: 'def', price: rand * 100)
items.insert(name: 'ghl', price: rand * 100)
puts "Item count: #{items.count}"
puts "The average price is: #{items.avg(:price)}"
```

```
squel sqlite://test.db
DB[:countries].where(:region => 'Middle East').avg(:GDP)
SELECT avg(GDP) FROM countries WHERE region = 'Middle East'
middle_east = DB[:countries].where(:region => 'Middle East')
middle_east.order(:name).each{|r| puts r[:name]}
middle_east.map(:name)
middle_east.map([:id, :name])
middle_east.as_hash(:name, :area)

require 'sequle'
DB = Sequel.connect('sqlite://blog.db')

DB = Sequel.connect('postgres://user:password@host:port/database_name')
DB = Sequel.connect("postgres:://user.password@host:port/database_name",
  max_connections: 10, logger: Logger.new('log/db.log'))
DB = Sequel.connct(adapter: postgres, user: 'user', password: 'password', host: 'host', port: port,
  database: 'database_name', max_connections: 10, logger: Logger.new('log/db.log'))
Sequel.connect('postgres://user:password@host:port/database_name'){|db| db[:posts].delete}

DB.run("create table t (a text, b text)")
DB.run("insert into t values ('a', 'b')")
dataset = DB['select id from items']
dataset.count
dataset.map(:id)
DB['select * from items'].each do |row|
  p row
end
name = 'Jim'
DB['select * from items where name = ?' name].each do |row|
  p row
end
posts = DB.from(:posts)
posts = DB[:posts]
posts.all
posts.each{|row| p row}
names_and_dates = posts.map([:name, :date])
old_posts, recent_posts = posts.partition{|r| r[:date] < Date.today - 7}
posts.order(:id).first
# SELECT * FROM posts ORDER BY id LIMIT 1
posts.frist
# SELECT * FROM posts LIMIT 1
posts.order(:stamp).last
# SELECT * FROM posts ORDER BY stamp DESC LIMIT 1
posts.first(:id => 1)
# SELECT * FROM posts WHERE id = 1 LIMIT 1
post.where(:id => 1).get(:name)
# SELECT name FROM posts WHERE id = 1 LIMIT 1
my_posts = post.where(category: 'ruby', author: 'tky')
# WHERE ((category = 'ruby') AND (author = 'tky'))


```

