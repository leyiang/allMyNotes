资源加载的正确做法：

```php
function toFriend_assets() {
    wp_enqueue_style( 'general', get_stylesheet_directory_uri() . '/css/general.css' );
    wp_enqueue_script( 'app', get_stylesheet_directory_uri() . '/js/app.js' );
}
add_action( 'wp_enqueue_scripts', 'toFriend_assets' );
```

其中 ``` get_stylesheet_directory_uri(); ``` 返回当前主题路径。



### 文章遍历的做法：

``` php+HTML
if( have_posts() ) :
    while( have_posts() ) : the_post(); ?>
        <h2><a href="<?php the_permalink(); ?>"><?php the_title(); ?></a></h2>
        <p><?php the_content(); ?></p>
    <?php endwhile;

else :
    echo "No post found!";

endif;
```

```php
 have_posts(), the_post(), the_title(), the_content()
```

	

### 获取头部、尾部的做法：

``` php
get_footer(), get_header() 会将主题目录下的 header.php 与 footer.php 中的代码加载。
    
在header.php 与 footer.php 中我们通常使用wp_head() 与 wp_footer() 载入wordpress必要的设置与资源。
    
language_attributes(); 设置网站语言
    <?php language_attributes() ?>
    
body_class(); 为body标签设置 Wordpress 需要的类名
    <body <?php body_class(); ?>>
```



### bloginfo() 常用参数：

```php
bloginfo('charset'): 字符集设置
    <meta charset="<?php bloginfo('charset');?>">
    
bloginfo('name'): 返回网站标题
    <title><?php bloginfo('name'); ?></title>

bloginfo('description'): 返回网站标语
    
bloginfo（'home') 或 bloginfo('siteurl'): 返回网站地址
    == echo home_url();
```



### Wordpress Nav Menu

```php
wp_nav_menu( [$args] );
想要 ul 标签有指定的class可以设置$args里menu_class的值

可以设置不同的theme location以在不同的位置显示不同的菜单：
    $args = array( 'theme_location' => 'location_name' );
	wp_nav_menu( $args );

使用theme location 之前需要在 functions.php 里先注册：
    register_nav_menus( array(
    	'location_name' => __('full name')
    ) )
    
    add_action( 'after_setup_theme', 'function name' );
之后再到后台菜单中设置不同的location对应的菜单即可
    
Wordpress在被选中的li上会加一个current-menu-item类，可以使用
    .current-menu-item a {
    	//...
	}
```



### Wordpress 自定义Page 页面

