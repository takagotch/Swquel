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

```

