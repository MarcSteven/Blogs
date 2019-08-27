# difference between rails and bin/rails?
- [Author]:[Marc Steven](https://twitter.com/marcstevencoder)
- 
Sometimes , you will use rails or bundle exec rails, and the other time you will use rails. Why that?

When you are already inside a Rails app, you should use bin/rails and bin/rake. These commands are created when Rails generates your app, and the command will use the right version of rails for that app. In Rails4.1 or higher, bin/rails will probably be faster than running bundle exec rails.

But when you don't have a Rails app, those files in bin/ don't exist yet, so   you cannot use them.

So, when you are inside a Rails app, use bin/rails.

When you are trying to create a new Rails app, use **rails**.

