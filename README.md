# Laravel 

<p align="center"><img src="https://res.cloudinary.com/dtfbvvkyp/image/upload/v1566331377/laravel-logolockup-cmyk-red.svg" width="400"></p>


List of basic and useful commands for laravel 6 framework.<br>
I am going to update this list every time I'll use a new command for my projects.

### Create a project
```
laravel new projectname
```

### Run local server
```
php artisan serve
```

### Make a controller
```
php artisan make:controller ControllerName
```

### Make a resourceful controller
```
php artisan make:controller ControllerName -r
```

### Make a resourceful controller and a related model
```
php artisan make:controller ControllerName -r -m ModelName
```

### Create new migration
```
php artisan make:migration create_tablename_table
```

### Do a migration
```
php artisan migrate
```

### Undo a migration
```
php artisan migrate:rollback
```

### Refresh a migration
```
php artisan migrate:fresh
```
(Drops all tables, use it carefully)

### Add column(s) to existing table
```
php artisan make:migration add_columnname_to_tablename
```
(Best practice to add a column)

### Make a model
```
php artisan make:model ModelName
```
(The model name should be the singular form of the DB table name)

### See all routes
```
php artisan route:list
```

### Add a route
Inside the file routes/web.php
```
Route::get('url', 'HomeController@index')->name('home.index');
```

### Add a route group with localization
Inside the file routes/web.php
```
Route::group([
    'prefix' => '{locale}', 
    'where' => ['locale' => '[a-zA-Z]{2}'],
    'middleware' => 'setlocale'], function() {
    
    Route::get('/', 'HomeController@index')->name('home.index');
    
});
```
Create a middleware to handle the language at every request
```
php artisan make:middleware SetLocale
```
This command will generate app/Http/Middleware/SetLocale.php, where we need to add only one line of code:
```
class SetLocale
{
    public function handle($request, Closure $next)
    {
        app()->setLocale($request->segment(1));
        return $next($request);
    }
}
```
we need to register this class in app/Http/Kernel.php to the $routeMiddleware array:
```
protected $routeMiddleware = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    // ...
    'setlocale' => \App\Http\Middleware\SetLocale::class,
];
```
We need to add another line to routes/web.php – to redirect the user from non-localed homepage to /en/ homepage. This route will be outside of the Route::group() we created above:
```
Route::get('/', function () {
    return redirect(app()->getLocale());
});
```
With this setup our links will look like this:
```
<a href="{{ route('home.index', app()->getLocale()) }}">home</a>
```
If you need to pass parameters to the controller:
```
<a href="{{ route('home.show', [app()->getLocale(), 'key' => 'value']) }}">Click me</a>
```
You need to pass "locale" parameters to controller too:
```
public function show($locale, ExampleClass $class) {
    $params = ExampleClass::latest()->get();
    return view('home', compact('params'));
}
```

### Add translation to Eloquent model
To translate database fields you could use the column approach (field_it, field_en), but it's not scalable and you'll need to tweak the database everytime you need a new language.<br>
From the terminal add this package:
```
composer require astrotomic/laravel-translatable
```
After installing the package we copy the config file to our project:
```
php artisan vendor:publish --tag=translatable
```
For every model we want to translate we need a ModelTranlslation:
```
php artisan make:model ModelNameTranlslation
```

Let's create the migrations for an Article table and it's tranlsation table.

```
php artisan make:migration create_article_translations_table
```


```
Schema::create('article', function(Blueprint $table) {
    # fields not translatable
    $table->increments('id');
    $table->timestamps();
});
```

```
Schema::create('article_translations', function(Blueprint $table) {
    $table->increments('id');
    $table->integer('article_id')->unsigned();
    $table->string('locale')->index();
    # translatable fields
    $table->string('title');
    $table->text('content');

    $table->unique(['article_id', 'locale']);
    $table->foreign('article_id')->references('id')->on('posts')->onDelete('cascade');
});
```
The translatable model Post should use the trait Astrotomic\Translatable\Translatable. The default convention for the translation model is PostTranslation. The array $translatedAttributes contains the names of the fields being translated in the PostTranslation model.
Inside the Article model:
```
use Astrotomic\Translatable\Contracts\Translatable as TranslatableContract;
use Astrotomic\Translatable\Translatable;

class Post extends Model implements TranslatableContract
{
    use Translatable;
    
    public $translatedAttributes = ['title', 'content'];
}
```
Inside the ArticleTranslation model:
```
class PostTranslation extends Model
{
    public $timestamps = false;
    protected $fillable = ['title', 'content'];
}
```

### Retrieve element by slug
Sometimes you'll need to change the default primary key (ID) to something else for example "slug".<br>
Inside the model file:

```
public function getRouteKeyName() {
    return 'slug';
}
```
### Route-model binding
With route model binding you can retrieve a record without specifying a ORM query inside your controller file.<br>
PS. If the route is inside a route group (for localizations for example) you need to pass $locale param too.
```
public function show($locale, Class $class) {
    return view('example-view', compact('class'));
}
```
### Create a HasMany relationship between to classes
Instead of using the "get where" statement you can use the built in HasMany method.<br>
For example a "section" can have different "categories".<br>
Inside the Section model:
```
public function categories() { 
    return $this->hasMany(Category::class, 'slug_section', 'slug'); 
}  
```
You can call this method from the SectionController in this way:
```
public function show($locale, Section $section) {
    $categories = $section->categories;
    return view('categories', compact('section', 'categories'));
}
```
PS. to use this approach you need to specify a database relationship inside your "category" migration file.
```
public function up() {
    Schema::create('categories', function (Blueprint $table) {
        $table->increments('id');
        $table->string('slug_section');
        ...
        ...
        $table->foreign('slug_section')
            ->references('slug')->on('sections')
            ->onDelete('cascade');
    });

}
```

### Create a Mail class
```
php artisan make:mail MailPurpose
```

### Create a markdown mail class
```
php artisan make:mail MailPurpose —markdown=path.of.the.view
```

### Import vendor assets to own project
```
php artisan vendor:publish —tag=wanted.asset
```
(Example laravel-mail)
