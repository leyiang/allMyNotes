# PHP PDO In Use

### 1. Set DSN

DSN(Data Source Name) contains information to connect the database.

##### Format

```php
{dbtype}:dbname={dbname};host={dbhost};port={dbport}
```

##### Example

```php
$host = "localhost";
$user = "root";
$password = "";
$dbname = "test";

$dsn = "mysql:host={$host};dbname={$dbname}";
```



### 2. Create a PDO instance

##### Format

```php
$pdo = new PDO($dsn, $user, $password);
```



### 3. Query something using PDO

##### Format

```php
// $stmt stands for statement
$stmt = $pdo->query("SELECT * FROM posts");

// PDO::FETCH_ASSOC is a fetch style
// Different style will have different return
// PDO::FETCH_ASSOC will return array indexed by column name
// Find the full list at https://www.php.net/manual/en/pdostatement.fetch.php
while( $row = $stmt->fetch(PDO::FETCH_ASSOC) ) {
    // title is one of the column names
    echo $row['title'];
}
```



##### Fetch using PDO::FETCH_OBJ style

```php
while( $row = $stmt->fetch(PDO::FETCH_OBJ) ) {
    // title is one of the column names
    echo $row->title;
}
```



##### Set default Fetch Style

```php
$pdo->setAttribute(PDO::ATTR_DEFAULT_FETCH_MODE, PDO::FETCH_OBJ);

...
while( $row = $stmt->fetch() ) {
...
```



##### Fetch All Method

```php

$stmt = $pdo->query("SELECT * FROM posts");

// Instead of looping through by fetch

//  while( $row = $stmt->fetch(PDO::FETCH_ASSOC) ) {
//    var_dump( $row );
//  }

// We can use fetchAll()
// It will return an array of all the result row
$res = $stmt->fetchAll(PDO::FETCH_ASSOC);
```



##### Get Row Count

The row count result is useful when we are check if a user exists or something else.

```php
$stmt = $pdo->query("SELECT * FROM posts WHERE author='{$author}'");
echo $stmt->rowCount();
```



### 4. Using PDO prepared statements

prepared will protect the SQL structure and prevent SQL injection.



##### Positional Parameters

```php
// author name might get from user input
// so we use prepare statements to prevent potential SQL injection
$author = "Scofield";


$sql = "SELECT * FROM posts WHERE author = ?";

// Instead of $pdo->query() we use $pdo->query();
$stmt = $pdo->prepare($sql);

// Notice in the execute method
// We pass an array
// The elements in this array will replace the ? in the $sql
// So they should be in the same order
$stmt->execute([$author]);
```



##### Named Parameters

```php
// Just like Positional Parameters
// But we change the ? to a colon and a name
$sql = "SELECT * FROM posts WHERE author = :author";
$stmt = $pdo->prepare($sql);

// Then in the execute method
// We pass an assoc array
// The order of this array is of course not important
$stmt>execute([
    'author' => $author
]);
```



##### Inserting Data using prepare statement

```php
// It is just as same as query info

$title = "Post Five";
$body = "This is post five";
$author = "Me";

$sql = "INSERT INTO posts(title, body, author) VALUES (:title, :body, :author)";
$stmt = $pdo->prepare($sql);

$stmt->execute([
    "title" => $title,
    "body" => $body,
    "author" => $author
]);
```



##### LIMIT doesn't work

```php
// IF we use the code below
// We will get nothing
$limit = 1;
$stmt = $pdo->prepare("SELECT * FROM posts LIMIT ?");
$stmt->execute([$limit]);

// We need to turn off the emulate prepare
// the MySQL driver has a bug/feature that will make it quote numeric arguments
// See addition info at https://stackoverflow.com/questions/10014147/limit-keyword-on-mysql-with-prepared-statement
$pdo->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);
```

