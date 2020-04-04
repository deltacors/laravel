# Laravel 
List of basic and useful commands for laravel 6 framework.
I'll update this list every time I'll use a new command for my projects.

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
