# Laravel Learning Notes

### Routes



#### Explain

We can find **/routes/web.php** in our Laravel project folder. It controls all the request from the browser.

```php
Route::get('/', function() {
   return view("welcome"); 
});
```

The program snippet above will show the **welcome view** when users link the root path.



#### List all the routes

```
php artisan route:list
```

This command will show you all the routes that are using right now.



***



### Views



**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**



If your Laravel not working as you defined

That is because the **cache**

```php artisan config:clear```



**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**



#### Explain

Views are eventually the things that will be rendered on the page. It contains HTML and other front-end codes.

In the very beginning of your project,  you can find a ``` /resources/views/welcome.blade.php ``` . The **blade** means the welcome view is using the **Blade Render Engine**



#### Usage

If you wanna use a view, you need to type the code like this:

```php
Route::get('/', function() {
  // return view("test.welcome") If the welcome view is in the test folder
   return view("welcome"); 
});
```

**Notice** that ```welcome``` refers to ```welcome.blade.php``` and we don't need to type the ```.blade.php```



```php
// Route::view($arg1, $arg2);
// $arg1 means the sub-path user type (like root path is / )
// $arg2 is the view name
Route::view("/", "welcome");
```

These two snippets above are doing the same thing.



#### pass data into view

The second argument of the view function allows you to pass some data to the view.

```php
Route::get("/", function() {
   
   // The data that you have
   $data = ["data1", "data2", "data3", "data4"]; 
    
   // In the second parameter
   // you will write a key-value array
   // where key is the var name you will use in the view page
   // and the value are the var you wanna pass
   return view("welcome", [
      "thedataname" => $data 
   ]);
});


// And in the View page, you can use the data we just passed like this:

<?php foreach( $thedataname  as $item ) : ?>
    <li> <?php echo $item; ?></li>
<?php endforeach; ?>
```

**Notice** that we don't usually use php statements to loop through a variable, we usually do as follow:

```php
@foreach ($thedataname as $item)
    <li> {{$item}} </li>
@endforeach
    
// This snippet does the same things as the code above does.
// It is powered by the blade engine
// The double curly braces are like <?php echo $item; ?>
  // Just for you to use php variables in HTML
```



#### Using Blade Layout

Suppose we have a ```layout.blade.php```

```php
/* ... */
/* Some header html code here **/

@yield("content");

/* Some footer html code here **/
/* ... */
```

In many cases, most our pages will have the same header and footer, so we usually put in one layout file,  and with ```@yield("content")```, we can insert external code later on.

In the ```home.blade.php```

```php
// The first step to use the layout.blade.php is to extend it
@extends("layout")
    
// Then we use @section to insert our code
// The "content" is what we put in the yield function, they should be the same
@section("content")
    <h1> HELLO </h1>
@endsection
```

The **second** argument of **@yield** will be the default value.

```php
@yield("title", "default title")

// If any sub view doesn't declare the title section
// Laravel will use "default title" automatically
```



We can pass short string into section in **one** line

```php
@section("title", "This is title")
    
The code above does the same things as the following code
    
@section("title")
    This is title
@endsection
```





If we have a separate ```nav.blade.php``` file, and we need to include it in our ```layout.blade.php```, here's how we can do it:

```php
@include("nav")

<div class="container">
    @yield("content")
</div>

```

**Notice** that the ```nav.blade.php``` and ```layout.blade.php``` need to be in the same folder. If it is not , we need to use ```folder.name``` syntax to access it.

```php
// File structure
//    /resources/views/test/nav.blade.php
//    /resources/views/layout.blade.php

// Here's code in layout.blade.php
@include("test.nav")
```

**And** we can pass some data through the **second** parameter of the include() function.

```php
@include("test.nav", [
    "username" => "testhaha"
])
    
    
// In the nav.blade.php
// We can use the $username directly
{{$username}}
```



#### Using Flashing Data

When a user did something, we need to give them feedback. To do this, we can use flashing data.

```php
// While return a redirect
// Using with statement

return redirect("contact")->with("message", "Message content here");

// And in the View
// Use this msg like this

@if( session()->has("message") )
	<div class="alert alert-success">
    	<strong>Success</strong> {{ session()->get("message") }}
    </div>
@endif
```



And there's another way to use flashing data

```php
session()->flash("message", "Message content here");
return redirect("contact");

// In the view page
// We use the message as above
```



***



### Controller

MVC stands for Model, View, and Controller. It is easier for us to maintain the code if we use MVC. Here's how we can use a controller in Laravel.

```php
// First, use this to create a controller
// You can find it at /app/HTTP/Controllers/CustomersController.php
// We usually capitalize the first letter as a naming convention
php artisan make:controller CustomersController
```

Once you've created the  controller, some code has already in the php file:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class CustomersController extends Controller {
	// TODO
}
```

Then we can add some code in this controller

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class CustomersController extends Controller {
    public function list() {
        $customers = [
            "John Doe",
            "Jane Doe",
            "Bob The Builder",
            "Another Name"
        ];

        return view("internals.customers", [
            "thedataname" => $customers
        ]);
    }
}

// What this code snippet does is bascially create an array
// and pass it into a view
```

Finally, in the route file ```/routes/web.php```, we change the second argument to the function name we declared in the controller

```php
Route::get("/customers", "CustomersController@list" );

// CustomersController@list
// @ means list is the function in the CustomersController
```



#### easy way to pass data

```php
// If we have 2 variables
$test1 = "Something";
$test2 = "ABCDEFGHI";

// And we pass it into our view
return view("customers", [
    "test1" => $test1,
    "test2" => $test2
])
    
// This way of doing this is very redundant
// The code below will do the same thing
return view("customers", compact("test1", "test2"));

// Just pass the var name, and it will pass it into the view
```



#### Middleware

Middleware is something that stands between the **request** and the **response**

There are two ways you can apply a middleware

First

```php
// In web.php

Route::resource("customers", "CustomersController")->middleware("auth");

// Just add the trailing ->middleware("auth")
```



**Or** you can put middleware in your controller

```php
public function __construct() {
	$this->middleware("auth");
}
```



##### Custom Middleware

First:

```php
php artisan make:middleware <name>
```

Find your Middleware in ```App\Middleware\<name>.php```



**register**

Open the ```App\Http\Kernel.php```



If you put your middleware in here:

```php
protected $middleware = [
    \App\Http\Middleware\TrustProxies::class,
    \App\Http\Middleware\CheckForMaintenanceMode::class,
    \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
    \App\Http\Middleware\TrimStrings::class,
    \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
];
```

Every single page will run your middleware;



If you put your middleware in here

```php
protected $middlewareGroups = [
    'web' => [
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        // \Illuminate\Session\Middleware\AuthenticateSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\VerifyCsrfToken::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ]
];
```

Every single page that locates in ```web route``` will hit your middleware



If you put your middleware here

```php
protected $routeMiddleware = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
    'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
    'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
    'can' => \Illuminate\Auth\Middleware\Authorize::class,
    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
    'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
];
// key=>value, key is your middleware name
```

Your middleware will be ran only if someone apply it ( Using ->middleware("name") );

***



### Model



#### Migration

If we run the command ```php artisan make:model Customer -m```, we will get a new model and a new **migration** file. We will talk about the model in a moment. Let's just focus on the migration file.

We can find the migration file in ```/database/migrations/2019_12_25_123123_xxxxx.php```

```php
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateCustomersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('customers', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('customers');
    }
}
```



Let's only **focus on the up()** function

```php
public function up()
{
    // This means to create a table in database
    Schema::create('customers', function (Blueprint $table) {
        
        // Roughly one row in this function
        // Means one column in the database
        // See all the column types in https://laravel.com/docs/5.8/migrations
        // under the Available Column Types
        
        $table->bigIncrements('id');
        $table->timestamps();
        
        // Lets add a new column in our database
        // "name" will be our column name in database
        $table->string("name");
    });
}
```



Once we changed the migration file, if we need it to be **applied in the database**, we need to run the migrate command

```php artisan migrate```



#### Tinker

Tinker is a shell for us to try our ideas very **quickly** and **easily**.

We use it by type the command: ```php artisan tinker```

In the previous section, we defined a **Customer model and migration**, let's try to access it in the tinker.

```php
// Customeris the model name
Customer::all();

// the all() method will return all the data from the database that related to this Customer model
// The all() method is inherited from the Model class
```

In this case, the command output nothing but a empty square brace, that's **because** we didn't insert data in the Customer table.

Let's try to insert some data in tinker

```php
$customer = new Customer();
$customer->name = "John Doe";
$customer->save();
```

This snippet above will insert a new record in the Customer table.



#### Use model method in Controller and pass it into our view

We can use model method in controller to reach the data from the database, then pass it to our view to fully fulfil the MVC.

**In the controller**

```php
<?php

namespace App\Http\Controllers;

use App\Customer;
use Illuminate\Http\Request;

class CustomersController extends Controller {
    public function list() {
        
        // This sentence here use the Customer model's method
        // to get all the data in the Customer table
        $customers = Customer::all();
		//Don't forget to add namespace
        
        return view("internals.customers", [
            "customers" => $customers
        ]);
    }
}
```

**And use it in the VIEW **

```php
    <ul>
        @foreach( $customers as $customer )
            <li> {{$customer->name }} </li>
        @endforeach
    </ul>
```



#### Let user add data in our database

First of all, add some form in the view

```php
    <form action="customers" method="post" class="pb-5">
        @csrf
        <input type="text" name="customer_name">
        <button class="btn" type="submit">Add customer</button>
    </form>

// @csrf will protect us from being attacked by csrf
// We need to use post method here
```



Second of all, change the route file

```php
Route::post("/customers", "CustomersController@store");
```



When we get **post** data at the ```/customers``` page, the store() method of Customers Controller will be called.

So we need to define the **store()** method in our customers controller.

```php
public function store() {
    $customer = new Customer();
    
    // the "customer_name" need to be identical with the name attribute value of the input in the view
    $customer->name = request("customer_name");
    $customer->save();

    // return back will return to the previous page
    return back();
}
```



##### Validate the data

If we use the data that users pass in directly, we might get some useless info. So we need to **validate** the data.

To do it, we need to add some code in the **store()** method

```php
    public function store() {
        
        /** NEW**/
        /** NEW**/
        $data = request()->validate([
            // Name can not be null
            // See full validation type
            // https://laravel.com/docs/5.7/validation#available-validation-rules
           'name' => 'required'
        ]);
        /** NEW**/
        /** NEW**/
      	
        $customer = new Customer();
        $customer->name = request("name");
        $customer->save();

        return back();
    }
```



Then we can display the error message in View

```php
<div class="msg msg-danger">
{{ $errors->first("name") }}
</div>
```

The **name** in ```$errors->first("name")``` need to be the same as the input name value.

Say I have a input ```<input name="test">```

I need to write this ```{{ $errors->first("test")}}```



##### Keep the old data

If one  of the inputs are wrong, suddenly, all the inputs will lose their value.

We need to keep the value even after Laravel catches error and redirects back

Here's what we can do:

```html
<input type="text" name="test" value="{{old("test")}}">
```

In the value attribute we will put ```old("test")```, the ```test``` is the value of **name** attribute



### Eloquent



#### Using where clause

```php
// In the examples above, we fetch all the data in database
Customer::all();

// However in some cases, we don't want all the data
// We want, say, customers whose id equals to 10
// This is how we can do it
Customer::where("id", 10);

```



#### Scope In Laravel

We can use where clause in our Controller like this:

```php
// Find all the customers that active equals to 1
$activeCustomers = Customer::where("active", 1)->get();
```

However, it's a little **hard** to read. We can use **scope** in Laravel to cope with this.



```php
// In our Customers Model:
// We **have** to follow the naming convention
// start with `scope` and follow by your function name
// the first letter of your function name need to be capitailzed
public function scopeActive($query) {
    return $query->where("active", 1);
}

// In the Controller
// Just call the function name and without capiatlize	
$activeCustomers = Customer::active()->get();

```



#### Simple way to Insert a row

In examples above, we use these code in our controller to insert data:

```php
public function store() {
    $data = request()->validate([
        'name' => 'required|min:3',
        'email' => "required|email",
        "active" => "required"
    ]);

    $customer = new Customer();
    $customer->name = request("name");
    $customer->email = request("email");
    $customer->active = request("active");
    $customer->save();

    return back();
}
```

**However**, this is very redundant.

```php
// the data attribute has all the request
// Like name, email
$data = request()->validate([...]);

// We can simply use this code to create a row
Customer::create( $data );
```

And the whole function would be like:

```php
public function store() {
    $data = request()->validate([
        'name' => 'required|min:3',
        'email' => "required|email",
        "active" => "required"
    ]);

	Customer::create( $data );
    
    return back();
}

// However if we run the code directly, Laravel will throw an exception
// That's because Laravel is protecting us from passing an array to create a row
// If we want to do this whatever
// We need to add some code in the **Cusotmer.php** the model file

// 1) We can use $fillable array to tell Laravel don't throw exception if we use ::create function to create a row
$fillable = ["name", "email", "active"];
// The value in $fillable is the column we want to fill

// 2) We can use $guarded array to tell Laravel what is forbidden
// If we leave it empty, then laravel will accept everything
$guarded = [];
```



#### The hasMany() & belongsTo() relation

In our project, we want to give every customer a company attribute. **Then** we can  say oh you are from Google, you are from apple.

To do this, we need to create a **Company Model**

```php
php artisan make:model Company -m
```



**Then**, let's change the migration of the company model

```php
// We add a name and a phone
Schema::create('companies', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->string("name");
    $table->string("phone");
    $table->timestamps();
});
```



After that, **migrate** the database

```php
// This command will make you lose all your data
php artisan migrate:fresh
```



Now that we have a **Company Model and Company table**, let add some data

```php
php artisan tinker

// To use this command
// Don't forget to set the protected $guarded to empty
$c = Company::create(["name"=>"ABC Company", "phone"=>"123-123-1234"])
```



Right now we have **two models**(Customer & Company), let's link them together

1) In the Company Model, add a function called ```customers```

```php
public function customers() {
    // Customer::class is the model you want to link
    return $this->hasMany(Customer::class);
}
```

This function will return **ALL** the customers that belongs to this company



If we run it in the tinker, we will get an error

```php
$c = Company::first()
$c->customers
// Notice that we call the customers as a property, not a method
    
// We will get
//Illuminate/Database/QueryException with message 'SQLSTATE[42S22]: Column not found: 1054 Unknown column 'customers.company_id' in 'where clause' (SQL: select * from `customers` where `
```



It means in the ```Customer Migration``` We need to  have an column called ```company_id``

Let change the migration and try it.

```php
/* ... */
$table->unsignedInteger('company_id');
/* ... */
```

Try run the command again you will notice it works.



In the Customer model, we can also add a function called ```company```

```php
public function company() {
    return $this->belongsTo(Company::class);
}
```

It will return the company it belongs to.



#### Accessor

While we using some properties, we may need to pre-process it

```php
// This will output 0 or 1
{{ $customer->active }}

// We want it to be Inactive or Active
// So we can do this:
{{ $customer->active ? "Active" : "Inactive" }}
```

However, there is a **even better** way to do this.

In the **Customer Model**

```php
// Naming convention: get + {ATTR NAME} + Attribute
// $attribute parameter is the value of this attribute
public function getActiveAttribute($attribute) {
	return [
		0 => "Inactive",
		1 => "Active"
	][$attribute];
}
```



#### RESTful controller

Basically we need to follow some pattern in order to make our code cleaner.

See at https://laravel.com/docs/6.x/controllers#resource-controllers



```php
Route::get("/customers/{customer}", "CustomersController@show")
    
// If we access the path : /customers/123
// We can get a variable called $customer with value 123
```



We can display the user detail info

In the **controller**

```php
public function show($customer) {
    // the $customer are the var in the path
    // We can use find() method to find the user's info
    // It requires the id
    $user = Customer::find($customer);
    return view("customers.show", compact("customers"));
}

// In this way, the `show` view can get the customer info
```

However, if we access a path that is not a **valid** user id like ```/customers/399``

Our page will break completely, to fix this:

```php
public function show($customer) {
	// We use this to prevent our page from break
    $user = Customer::where("id", $customer)->firstOrFail();
    return view("customers.show", compact("customers"));
}
```



**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**



If your Laravel not working as you defined

That is because the **cache**

```php artisan config:clear```



**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**



**Attention**

In Laravel, if the name defined in ``web.app`` is the same in ``controller``, we can use short hand method

```php
// In the web.php
Route::get("/customers/{customer}", "CustomersController@show")
// Notice what we put in the curly braces: customer
    
    
// In the controller
public function show(Customer $customer) {
    return view("customers.show", compact("customer"));
}
```

It will do the same as the **where** clause does



**After** You complete all the actions the **RESTful API** described, you will have this many routes:

```php
Route::get("/customers", "CustomersController@index" );
Route::post("/customers", "CustomersController@store");
Route::get("/customers/create", "CustomersController@create" );
Route::get("/customers/{customer}", "CustomersController@show");
Route::get("/customers/{customer}/edit", "CustomersController@edit");
Route::patch("/customers/{customer}", "CustomersController@update");
Route::delete("/customers/{customer}", "CustomersController@destory");
```

However if you follow **all** the rules RESTful says, you can using one line code to exchange it

```php
//Route::get("/customers", "CustomersController@index" );
//Route::post("/customers", "CustomersController@store");
//Route::get("/customers/create", "CustomersController@create" );
//Route::get("/customers/{customer}", "CustomersController@show");
//Route::get("/customers/{customer}/edit", "CustomersController@edit");
//Route::patch("/customers/{customer}", "CustomersController@update");
//Route::delete("/customers/{customer}", "CustomersController@destory");


/** Using This **/
/** Using This **/
/** Using This **/
/** Using This **/

Route::resource("customers", "CustomersController");
```



And while you create a new controller, you can let Laravel to create all the **method(index, create, store)** for you.

```php
// Where test is the model name
// you can omit it
php artisan make:controller TestController -r -m Test
```



### Handy Laravel Features

#### Auth

Most websites will have login and register. If you want your websites to have it, Laravel done the most part of it for you.

You just need to run the command

```php
php artisan make:auth
```



**In this way**, you will have many pre defined **view & controller & model** available for you.

You can just simply change it. If you don't know which **Route** the auth goes. Don't forget to use

```php
php artisan route:list
```



#### Url helpers

* ```url()```

  ```php+html
  <form action="{{ url("/contact") }}" method="POST">
  ...
      
  // The url("/contact") will eventually become
  => http://localhost/laravel/first/contact (Only in my case)
      
  However if we put "/contact" without url helper in form action
  => http://localhost/contact
  
  We will get this
  And this is not what we want
  ```

* Named routes ``route()`` (preferred)

  ```php
  // How to add a name to a route
  Route::get("contact", "ContactForm@create")->name("contact.create");
  
  // Just add the tailing ->name() to the route
  // the parapeter in it is the name you gave the route
  // By convention we call it `path.method`
  
  //Then in the view
  {{ route("contact.create") }}
  // This line of code can get the route that we named
  
  // If you are using the RESTful api
  Route::resource("customers", "CustomersController");
  // Laravel already named all the routes for you
  // You can see it by using the command: 
  php artisan route:list
      
      
      
  // If we wanna pass data in url
  // Like /customers/1 where 1 is the id of the customer
  // In the route function, we can do this
  route("contact.create", ["customer" => $customer]);
  // The $customer is a variable that stores the $customer
  // Laravel is smart enough to find which attribute do you want
  // In the route you write like this: customers/{customer}
  // This {customer} represents the ["customer" => $customer] in the quotation mark
  ```

* ```action()```

  ```php
  // We can just simple pass the Controller@method into action
  action("CustomerController@index");
  // It will automatically find the controller@method in our web.php and find which path it belongs to
  
  // In this way, your IDE might not get the definition of your Controller (Ctrl + click on the name)
  // We can use an alternative way to do this
  action([ \App\Http\Controllers\CustomerController::class, 'index']);
  // This code does the sme thing as the code above
  // However it enables you do find the Controller definition quicly
  ```



#### Add Laravel custom artisan command

```php
php artisan make:command <Command Name>
```

After you ran this code, you can find your `Command Name.php` in the `\App\Console\Commands\` Folder.

##### Signature

The signature is what your command will look like.



If you setup your signature like this
You will type `php artisan product:add` in your command

```php
protected $signature = 'product:add';
```



This is how your add a **required** argument

```php
protected $signature = 'product:add {name}';
```

This is how your add an **optional** argument

```php
protected $signature = "product:add {name?}"
```

Or

```php
protected $signature = "product:add {name=haha}"
```



##### The description

```php
protected $description = 'Add a dummy product quickly.';
```

The `$description` will be displayed if you use `help` command



##### The handle function

The handle function will be fired when you run `php artisan product:add`

```php
public function handle() {
    $product = Product::create([
        "name" => $this->argument("name"),
        "user_id" => 1,
        "description" => $this->argument("description"),
        "price" => 123.2,
        "stock" => 10
    ]);
}
```

You can do **Anything** in your handle function.

**Notice** How we use the argument that passed in through the command line.

```php
$this->argument("name");
```



##### Return infos

In the handle function, you can return info by using function below

```php
$this->info("Added " . $product->name);
//$this->warn("Product created successfully");
//$this->error("Product created successfully");
```



##### Get info from user

```php
$name = $this->ask("What is the product name");
```

While users run the `php artisan make:product`, the code above will ask users the product name. And the value will store in the `$name` variable.



##### Confirm

```php
if( $this->confirm("Are you ready to insert?") ) {
    // Insert codes...
}
```

Our command will ask  users if they are willing to run the code.

If so, the ``Insert Codes`` will be fired.

##### Create command in console.php

Sometimes a command only do a little things. We don't need it to be a **single** class. We can put it in console.php

```php
/app/routes/console.php
```

Add code like this:

```php
Artisan::command("test", function() {
    $this->info("hahahaha");
});
```

The first argument is the **name of the command**, the second argument is basically the **handle function**

So if you run `php artisan test`, you will get `hahahaha`



If you wanna pass **description**, do this

```php
Artisan::command("test", function() {
    $this->info("hahahaha");
})->describe("This is a command just for testing.");
```



You can also use **argument**

```php
Artisan::command("test {name}", function() {
    $name = $this->argument("name");
    $this->info( $name );
});
```



**Fancy syntax**

```php
Company::whereDoesntHave("customers")
    ->get()
    ->each( function($company){
        $company->delete();
        $this->warn("Deleted." . $company->name);
    });
```



### Using Node.js In Laravel

#### Install

```php
// First of all, make sure the node and npm is intalled in your computer
// You can run node -v and npm -v
// The return value should be some kind of version string

npm install
// This command will install all the dependencies that you specify in the package.json file
    
// After the npm install has completed, a new folder has been created that is called node_modules, In this folder npm store tons of thousands of packages for us
```



#### Compile

In the ```/resources/js```, we have an ```app.js```, we can **write** and **include** our JS code in here.

And in the ```/resources/sass```, we can find a ```app.scss```, we can **write** and **include** our CSS code in here. 

**NOTICE**  we **need** to run ``npm run dev``  so our ``js`` and ``sass`` will be compiled and put in the ```/public/js``` and ```/public/css``` respectively.


##### npm run watch

If we are developing our website, and we need to patch our ``JS`` and ``CSS`` code now and then, we can ``` npm run watch```, so that every time you save the file, npm will re-compile the file again.



#### ERRORS

Sometimes we run ```npm run dev``` or ```npm run watch``` will encounter many **ERRs**. To fix this, we need to delete all the ``/node_modules`` folder and then re-run the ```npm install```

```rm -rf node_modules && npm install```



### Events

#### Intro

Typically when a user register to our websites. We will do the following steps:

```php
1. Send a welcome email
2. Add the user to a subscribe list
3. Notify the admin
```

We can put all of this steps in to your Controller in the store method.

**However**, we don't usually do this.

We need to use **Event** and **Listener**.



#### Create a Event

```php
php artisan make:event TheNameOfYourEvent
```

Then you can find your event in the ```/App/Events``` folder



#### Use this event

```php
// In your controller
public function store() {
    ...
      
	event( new TheNameOfYourEvent($something) );
	...
}
```

**Notice** the ``$something`` is the variables you wanna pass in to the event. And you need to receive it in your event file like this:

```php
// You can set $something as private
// But if you set as public
// You can use $something directly in your listener
public $something;

public function __construct($something) {
    $this->something = $something;
}
```



#### Create Listener

If you try to register a new user, you will find out that nothing novel happens. That's **because** we didn't add any listener yet.

Consider the **Event** as things what **Users** done( like register ), and the **Listener** are the things **We as the website** would do (Like send a welcome email )**AFTER** users triggered some events.

```php
php artisan make:listener TheNameOfYourListener
```

Then you can **find** your listener in the ```/App/Listeners``` folder.

We can put our code in  the **handle()** method

```php
public function handle($event) {
    // anything
}

// If you wanna use the $something we passed into the event file
// Do it like this:
$event->something;
```



#### Map Event and Listeners

If you try to registered a new user again, you will notice again that the things still work the **same**.

That's because we didn't **map** the **event** and **listener**.

Find the ```/App/Providers/EventServiceProvider.php```, then add our mapping code in the **listen** method like this:

```php
protected $listen = [
    ...,
    TheNameOfYourEvent::class => [
     	\App\Listeners\TheNameOfYourListener::class  
    ];
]
```

That will complete the whole process.



If we add a new line of listener that **doesn't** exists in our folder like this

```php
protected $listen = [
    ...,
    TheNameOfYourEvent::class => [
     	\App\Listeners\TheNameOfYourListener::class,
        \App\Listeners\DoesNotExistListener::class
    ];
]
```

And run the **command** 

```php
php artisan event:generate
```

You will notice that the Laravel actually does this for us. So this can be a shortcut when we declare a new Listener.



**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**



If your Laravel not working as you defined

That is because the **cache**

```php artisan config:clear```



**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**

**VERY IMPORTANT!**



### Laravel Queue

While we are sending an email to our user. The page is not doing anything, and that give user a bad experience. Nevertheless, we can use **queue** to reduce the bad experience.

#### Create Queue table

```php
php artisan queue:table
php artisan migrate
```

First we need to use the snippet above to create a queue table

#### Implement the ```ShouldQueue```

In the Listeners that we don't want them to run immediately, **implement it with `ShouldQueue`**

```php
class WelcomeNewCustomerListener implements ShouldQueue {

    public function handle($event) {
        Mail::to( $event->customer->email )->send( new WelcomeNewUserMail());
    }
}
```

Don't forget to configure the `.env file` in your project fold, change the `QUEUE_CONNECTION=sync` to `QUEUE_CONNECTION=database`

#### Run Queue Tasks

Then if you trigger your event again, you will notice that the listeners you implemented as `ShouldQueue` is not working.

However if you go check the `job` table that the `queue:table` created, you will notice that there's a new  row of information has been inserted.

To run this, you need to run:

```php
php artisan queue:work
```

This will monitor the `job` database, every time there's job for it to do, it will do it and delete the job from the `job` database.



If you add an ampersand after the code, you can make the `queue:work` work in the background

```php
php artisan queue:work &
    // However it doesn't work for my computer;
```

