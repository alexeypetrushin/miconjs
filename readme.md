Dependency injector, assembles application.

Registering, getting and setting components.

``` CoffeeScript
app.register 'component', -> 'some component'
console.log app.component
# => some component

app.component = 'another component'
console.log app.component
# => another component
```

Dependencies can be specified implicitly.

``` CoffeeScript
app.register 'a', ->
  console.log 'initializing a'
  'a'
app.register 'b', ->
  console.log 'initializing b'
  "#{app.a} b"

console.log app.b
# => initializing b
# => initializing a
# => a b
```

Dependencies can be specified explicitly. The only difference with implicit approach
is that explicit declaration will resolve circular dependencies (implicit approach
will fail).

``` CoffeeScript
app.register 'a', ->
  console.log 'initializing a'
  'a'
app.register 'b', dependencies: ['a'], ->
  console.log 'initializing b'
  "#{app.a} b"

console.log app.b
# => initializing a
# => initializing b
# => a b
```

Component lifecycle callbacks.

``` CoffeeScript
app.register 'component', ->
  console.log 'initialization'
  'some component'
app.before 'component', -> console.log 'before initialization'
app.after 'component', -> console.log 'after initialization'

app.component
# => before initialization
# => initialization
# => after initialization
```

Autoloading components from directory, same as manually
write `app.register 'something', -> require 'something'` for every file in
directory.

Provide `watch` option to reload on change.

``` CoffeeScript
app.register.directory directoryPath, watch: true
```

Scopes.

``` CoffeeScript
app.register 'params', scope: 'request', -> {}

startFiberSomehow ->
  app.scope 'request', ->
    app.params.key = 'some value'
    console.log app.params
    # => {key: 'some value'}
```

Scope callbacks.

``` CoffeeScript
app.beforeScope 'request', -> console.log 'before'
app.afterScope 'request', -> console.log 'after'

startFiberSomehow ->
  app.scope 'request', ->
  # => before
  # => after
```

Note: fiber and custom scopes will not work in browser because browsers doesn't support fibers.

Copyright (c) Alexey Petrushin, http://petrush.in, released under the MIT license.