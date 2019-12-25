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



***



### Views



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
// Customer may by the model name, I'm a little bit of confuse right now.
// I will update this note later.
Customer::all();

// the all() method will return all the data from the database that related to this Customer model
// I guess the all() method is inherited from the Model class
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

