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
my_posts = posts.where(stamp: (Date.today - 14)..(Date.today - 7))
# WHERE ((stamp >= '2018-10-02' AND (stamp <= '2018-10-03')))
my_posts = posts.where(category: ['ruby', 'postgres', 'linux'])
# WHERE (category IN ('ruby', 'postgres', 'linux'))
my_posts = posts.where{stamp > Date.today << 1}
# WHERE (stamp > '2018-10-03')
my_posts = posts.where{stamp =~ Date.today}
# WHERE (stamp = '2018-11-01')
my_posts = posts.where(Sequel[:stamp] > Date.today << 1)
# WHERE (stamp > '2018-10-02')
my_posts = posts.where(Sequel[:stamp] =~ Date.today)
# WHERE (stamp > '2018-11-01')
my_posts = posts.where(category: /ruby/i)
# WHERE (category ~* 'ruby')
my_posts = posts.exclude(category: ['ruby', 'postgres', 'linux'])
# WHERE (category NOT IN ('ruby', 'postgres', 'linux'))
my_posts = posts.exclude(category: ['ruby', 'postgres', 'linux'], id: 1)
# WHERE ((category NOT IN ('ruby', 'postgres', 'linux')) OR (id != 1))
posts.where(Sequel.lit('stamp IS NOT NULL'))
# WHERE (stamp IS NOT NULL)
author_name = 'TKY'
posts.where((Sequel.lit('(stamp < ?) AND (author != ?)', Date.today - 3, author_name))
# WHERE ((stamp < '2018-10-02') AND (author != 'TKY'))
DB[:item].where(Sequel[:price] > DB[:items].select{avg(price) + 100})
# WHERE (price > (SELECT avg(price) + 100 FROM items))
my_posts.each{|row| p row}
posts.where(Sequel.like(:category, '%ruby%')).count
# SELECT COUNT(*) FROM posts WHERE (category LIKE '%ruby%' ESCAPE '\')
max = DB:[:histroy].max(:value)
# SELECT max(value) FROM history
sum = DB[:items].sum(:price)
# SELECT sum(price) FROM items
avg = DB[:items].avg(:price)
# SELECT avg(price) FROM items
posts.order(:stamp)
# ORDER BY stamp
posts.order(:stamp, :name)
# ORDER BY stamp, name
posts.order(:stamp).order(:name)
# ORDER BY name
posts.order(:stamp).order_append(:name)
# ORDER BY stamp, name
posts.order(:stamp).order_prepend(:name)
# ORDER BY name, stamp
posts.reverse_order(:stamp)
# ORDER BY stamp DESC
posts.order(Sequel.desc(:stamp))
# ORDER BY stamp DESC
:stamp.desc
Sequel.desc(:stamp)
posts.select(:stamp)
# SELECT stamp FROM posts
posts.select(:stamp, :name)
# SELECT stamp, name FROM posts
posts.select(:stamp).slect(:name)
# SELECT name FROM posts
posts.select(:stamp).select_append(:name)
#SELECT stamp, name FROM posts
posts.where(Sequel[:stamp] < Date.today - 3).delete
# DELETE FROM WHERE (stamp < '2018-10-11')
# DO THIS:
posts.where(Sequel[:stamp] < Date.today - 7).delete
# NOT THIS:
posts.delete.where(Sequel[:stamp] < Date.today - 7)
posts.insert(category: 'ruby', author: 'david')
# INSERT INTO posts (category, author) VALUES ('ruby', 'tky')
posts.where(Sequel[:stamp] < Date.today - 7).update(state: 'archived')
# UPDATE posts SET state = 'archived' WHERE (stamp < '2018-10-02')
posts.where(Sequel[:stamp] < Date.today - 7).update(backup_number: Sequel[:backup_number] + 1)
# UPDATE posts SET backup_number = (backup_number + 1) WHERE (stamp < '2018-10-10')
# DO THIS:
posts.where(Sequel[:stamp] < Date.today - 7).update(:state => 'archived')
# NOT THIS:
posts.update(:state => 'archived').where(Sequel[:stamp] < Date.today - 7)

DB.transaction do
  # BEGIN
  posts.insert(category: 'ruby', author: 'tky')
  # INSERT
  posts.where(Sequel[:stamp] < Date.today - 7).update(:state => 'archived')
  # UPDATE
end
# COMMIT

DB.transaction do
  # BEGIN
  posts.insert(:category => 'ruby', :author => 'tky')
  # INSERT
  if posts.where('stamp < ?', Date.today - 7).update(:state => 'archived') == 0
  # UPDATE
    raise Sequel::Rollback
  end
end
# ROLLBACK

order_items = DB[:items].join(:order_items, item_id: :id).where(order_id: 1234)
# SELECT * FROM items
# INNER JOIN order_items ON (order_items.item_id = items.id)
# WHERE (order_id = 1234)
order_total = order_items.sum(:price)
# SELECT sum(price) FROM items
# INNER JOIN order_items  ON (order_items.item_id = items.id)
# WHERE (order_id = 1234)
items.where(x: 1)
# SELECT * FROM items WHERE(x = 1)
items.where(1 => :x)
# SELECT * FROM items WHERE(1 = x)"
items.where(x: 'x')
# SELECT * FROM items WHERE (x = 'x')
Sequel[:table][:column]
Sequel.quality(:table, :column)
# table.column
Sequel[:schema][:table]
# schema.table
Sequel[][][]
# schema.table.column
Sequel[].as()
Sequel.as()
# column AS alias
Sequel.as(DB[].slect{max(id)}, :p)
# (SELECT max(id) FROM posts) AS p
(Sequel[:column] + 2).as(:c_plus_2)
# (column + 2) AS c_plus_2
DB = Sequel.connect('sqlite://blog.db')
class Post < Sequel::Model
end
Post.tabel name # => :posts
class Post < Sequel::Model(:my_posts); end
# or:
class Post < Sequel::Model(DB[:my_posts]); end
class Post < Sequel::Model(DB[:my_posts].where(category: 'ruby')); end
class Post < Sequel::Model(DB[:my_posts].select(:id, :name).order(:date)); end
post = Post[123]
post.pk # => 123
class Post < Sequel::Model
  set_primary_key [:category, :title]
end
post = Post['ruby', 'hello world']
post.pk # => ['ruby', 'hello world']
Post.no_primary_key
post = Post.first(title: 'hello world')
post = Post.first(num_comments < 10)
DB[:posts].first.class # => Hash
Post.first.class # => Post
Post.where(category: 'ruby').each{|post| p post}
Post.where(num_comments < 7).delete
Post.where(Sequel.like(:title, /ruby/)).update(category: 'ruby')
post.values # => {:id => 123, :category => 'ruby', :title => 'hello world'}
post.id # => 123
post.title # => 'hello world'
post[:id] # => 123
post[:title] # => 'hello world'
post.title = 'hey there'
post[:title] = 'hey there'
post.save
post.set(title: 'hey', updated_by: 'foo')
post.update(title: 'hey there', updated_by: 'foo')
post = Post.new(title: 'hello world')
post.save
post = Post.create(title: 'hello world')
post = Post.new do |p|
  p title = 'hello world'
end
post = Post.create(|p| p.title = 'hello world')

class Post < Sequel::Model
  def after_create
    super
    author.increase_post_count
  end
  def after_destroy
    super
    author.decrese_post_count
  end
end

post.delete # => bypasses hooks
post.destroy # => runs hooks
Post.where(:category => 32).delete # => bypasses hooks
Post.where(:category => 32).delete # => runs hooks

class Post < Sequel::Model
  many_to_one :author
  one_to_many :comments
  one_to_one :first_comment, :class=>:Comment, :order=>:id
  many_to_many :tags
  one_throught_one :first_tag, :class=> Tag, :order=>:name, :right_key=>:tag_id
end

post = Post.create(name: 'hi!')
post.author = Author.first(name: 'tky')
post.author

post = Post.create(name: 'hi!')
post.comments
comment = Comment.create(text: 'hi')
post.add_comment(comment)
post.remove_comment(comemnt)
post.remove_all_commnents
tag = Tag.create(tag: 'interesting')
post.add_tag(tag)
post.remove_tag(tag)
post.remove_all_tags

post.comments_detaset.destroy
post.tags_dataset.where(subscribers: 0).order(:name).all

class Person < Sequel::Model
  one_to_many :posts, :eager=>[:tags]
end
class Post < Sequel::Model
  many_to_one :person
  one_to_many :replies
  many_to_many :tags
end
class Tag < Sequel::Model
  many_to_many :posts
  many_to_many :replies
end
class Reply < Sequel::Model
  many_to_one :person
  many_to_one :post
  many_to_many :tags
end
Post.eager(:persson).all
Post.where{topic > 'M'}.order(:date).limit(5).eager(:person).all
person = Person.first
person.posts
Post.eager(:persion, :tags).all
Post.eager(:person).eager(:tags).all
Tag.eager(posts: :replies).all
Reply.eager(persion: :posts).all
Person.eager(posts: {replies: [:persion, {tags: [:posts, :replies]}]}).all

Post.eager(replies: proc{|ds| ds.where(Sequel.like(text, '%foo%'))}).all
Post.eager_graph(replies: proc{|ds| ds.where(Sequel.like(text, '%foo%'))}).all
Post.eager(replies: {proc{|ds| ds.where(Sequel.like(text, '%foo%'))} => [:person, :tags]}).all
Post.association_left_join(:replies)
# SELECT * FROM posts
# LEFT JOIN replies ON (replies.post_id = posts.id)
Post.association_join(:author, replies: :person).all
# SELECT * FROM posts
# INNER JOIN authors AS author ON (author.id = posts.author_id)
# INNNER JOIN replies ON (replies.post_id = posts.id)
# INNNER JOIN people AS person (person.id = replies.person_id)
class Post < Sequel::Model
  dataset_module do
    where{num_comments < 30}
  end
  def clean_boring
    with_few_comments.delete
  end
end

Post.where(category: 'ruby').clean_boring
# DELETE FROM posts WHERE ((category = 'ruby') AND (num_comments < 30))
class Post < Sequel::Model
  dataset_module do
    where(:with_few_comments, Sequel[:num_commnets] < 30)
    select :with_title_and_date, :id, :title, :post_date
    order :by_post_date, :post_date
    limit :top10, 10
  end
end
Post.with_few_comments.with_title_and_date.by_post_date.top10
# SELECT id, title, post_date
# FROM posts
# ORDER BY post_date
# LIMIT 10

class Post < Sequel::Module
  def validate
    super
    errors.add(:name, "can't be empty") if name.empty?
    errors.add(:written_on, "should be in the past") if written_on >= Time.now
  end
end







```

