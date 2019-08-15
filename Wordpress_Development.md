### 资源加载的正确做法：

```php
function toFriend_assets() {
    wp_enqueue_style( 'general', get_stylesheet_directory_uri() . '/css/general.css' );
    wp_register_script( 'latest-jQuery', get_stylesheet_directory_uri() . '/js/jQuery.js' );
    wp_enqueue_script( 'app', get_stylesheet_directory_uri() . '/js/app.js', array('latest-jQuery') );
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

```PHP
is_page( $page );
$page 留空，判断当前是否为Page, $page 可填 相应page id 或 slug name，以判断是否是相应的page
    <?php is_page( 'sample_page' ); ?>
    
为单独的一个Page自定义页面：
   	在主题文件夹下建立 post-**.php, 其中**为该Page的id 或 slug name
    
为多个Page 自定义页面：
    在主题文件夹下建立 任意名称的 php, 在首行需要加 /* Template Name: ??? */
    编辑要使用这个模板的Page, 设置template即可
```



### Wordpress 子页面

```php
wp_list_pages( $args ); 显示所有符合 $args 规定的Page
$post 是 WP_POST 的对象，里面的许多重要的信息，比如 $post->ID
    
用wp_list_pages() 显示当前页面的子页面, 就需要在$args 中指定 'child_of' => $post->ID

指定'title_li'=>'' 可将第一个无用的li删除
    
自定义函数取回页面的ancestor ID
// Get top ancestor
function get_top_ancestor_id() {
    global $post;
    if( $post->post_parent ) {
        $res = array_reverse( get_post_ancestors( $post-> ID ) );
        return $res[0];
    }

    return $post->ID;
}

get_post_ancestors 返回一个数组
```



### 使用ID获取信息

```php
get_the_title( $id );
get_the_permalink( $id );

Notice: You need to output these manually
   .
```



### Post 元数据

```php
the_time( $args ); 显示当前post的发布时间, $args 遵循php data的格式
get_the_author(); 返回当前author
get_author_posts_url(); 返回对应ID的链接
get_the_author_meta( 'ID' ); 返回当前post Author的ID
get_the_category(); 返回当前post分类数组，每个元素都是一个WP_TERM对象
get_category_link( id ); 返回分类归档地址，
    
    <?= ?> ==== <?php echo... ?>
```



### Archive Page Template

```php
archive.php 控制Archive Page (日期归档、分类归档、标记归档、作者归档等)的页面。

is_category() 判断当前页面是否是 分类归档
    single_cat_title() 输出当前分类标题
    
is_tag() 判断是否是 标记（tag）归档
    single_tag_title() 输出当前tag标题
    
is_author() 判断是否是 作者 归档
    get_the_author() 获取作者
    但必须要先调用 the_post();
	最后用rewind_posts();

is_day() 判断是否是日期归档
    get_the_date() 获取相应日期
    
is_month() 判断是否是月份归档
    get_the_date('M. Y'); OR
    get_the_date('F Y');

is_year() 判断是否是年份归档
	get_the_date('Y');

```



### Excerpt

```php
Using get_the_excerpt(); will return the string, without creating any html tags.
    
Customize excerpt word length:
/* Customize excerpt length */
function toFriend_excerpt_length() {
    return 25;
}
add_action( 'excerpt_length', 'toFriend_excerpt_length' );


We can use ' if( $post->post_excerpt )' to  determine wheather the user set excerpt for the post.
```



### Wordpress Featured Images

```php
1. 首先需要使用 add_theme_support('post-thumbnails'); 开启Featured Image 的支持。
2. 可以放在setup 函数中，然后add_action( 'after_setup_theme', 'toFriend_setup' ); 
3. Use the_post_thumbnail() to display the img tag
4. 使用 add_image_size('size_name', width, height, auto_crop);
	add_image_size('small', 180, 120, true); OR
    add_image_size('small', 180, 120, array('left', 'top'));
	the_post_thumbnail('small');

如果有时 设置大小后调用 不生效，图片大小不正常的话，**重新上传**
也可以在wordpress 上传后手动裁切。

为img标签添加类名：
<?php the_post_thumbnail('small', array('class'=>'post-thumbnail')); ?>

使用has_post_thumbnail()判断是否有thumbnail
    
在php.ini中搜索max 或 8M 或 2M 找到上传大小的限制
```



### Wordpress Search Form

```php
get_search_form(); 获取搜索表单  
使用searchform.php 可以定制表单，默认：
<form style="display:inline-block;" role="search" method="get" id="searchform" class="searchform" action="<?= home_url( '/' ); ?>">
    <div><label class="screen-reader-text" for="s"></label>
    <input type="text" value="" name="s" id="s">
    <input type="submit" id="searchsubmit" value="Search"></div>
</form>
            
search.php 定制 搜索结果页面
the_search_query(); 输出用户搜索的关键词
```



### DRY Principle

``` php
Dont
Repeat
Yourself
    
get_template_part( $name ); 可获取指定模板
    $name 的 值是模板文件的名子
    get_template_part( 'content' )，此时主题文件夹内就必须有 content.php
```



### Post Format

```php
Wordpress 支持9种post format: aside, gallery, link, image, quote, status, video, audio, chat.
在setup函数中添加post-format的支持：
    add_theme_support('post-formats', array('aside', 'gallery', 'link'));
	array中是要支持的post format
	别忘了add_action( 'after_setup_theme', 'toFriend_setup' );


get_template_part( $name, $arg );
此时$arg 换成 get_post_format(), wordpress 就会根据不同的post format 调用不同的文件
    
    比如当前post format是aside, get_template_part( 'content', get_post_format() ) 就会调用content-aside.php
```

[Check here for more info](https://wordpress.org/support/article/post-formats/)



### Widgets

```php
使用Widgets之前必须先声明注册Widget location
    
/* Add Widget Locations */
function toFriend_widgets_init() {
    register_sidebar( array(
        'name' => 'Sidebar',
        'id' => 'siderbar1'
    ) );
}
add_action('widgets_init', 'toFriend_widgets_init' );

然后在指定的地点使用：<?php dynamic_sidebar('id'); ?>
使用 is_active_sidebar( $id ); 来判断 widget 是否开启。
```



### Customized Homepage

```php
新建两个页面，Homepage 和 Blog (名字无所谓)。
    Homepage 中 随便写点，Blog留空。
到Wordpress - dashboard - settings - reading,
	将Your homepage displays 设置为 A static page
        Homepage 选 homepage
        Postpage 选 blog
        
到主题文件夹中新建一个 front-page.php 自定义homepage
```



### WP_QUERY

```php
首先建立新对象：
$uncategorized = new WP_QUERY('cat=1&post_per_page=2&orderby=title&order=ASC');
WP_query($args), $args 也可以是array
    
    cat 对应的是category, 值是 category 的 id
    post_per_page 则见字面意思
    orderby = title 就是用标题排序，默认是Date
    order = ASC 是顺序排序，DESC则是逆序
    

if( $uncategorized-> have_posts()) :
while( $uncategorized->have_posts()) : $uncategorized->the_post(); ?>
    <h2><?php the_title(); ?></h2>
    <?php endwhile;
endif; ?>
    
在所有事情处理完后，一定要调用：wp_reset_postdata();
以免干扰正常以URL来循环的the_post();
```

[WP_QUERY](https://developer.wordpress.org/reference/classes/wp_query/)



### 分页

```php
首先到 Wordpress Settings 的Reading 里设置每页多少个post 默认是10 个
    
之后在代码的适合位置写上 previous_posts_link(), next_posts_link() 就OK了
也可以直接输出：echo paginate_links(); 这个有页面的数字选择

如果是用的WP_QUERY 来循环post，上述方法则无效
    正确方法：
    $currentPage = get_query_var( 'paged' );

    $posts = new WP_Query( array(
        'author_name' => 'admin',
        'posts_per_page' => 3,
        'paged' => $currentPage
    ) );

    if($posts->have_posts()) :
    while( $posts->have_posts() ) : $posts->the_post(); ?>
        <li><?php the_title(); ?></li>
        <?php endwhile;
    endif;

	get_query_var( 'paged' ); 用来获取当前的页面数
    *** Notice that in a **Static Front Page** We use 'page' instead of 'paged'
        
    然后再设置
        previous_posts_link('&lt;&lt;Previous ', $posts->min_num_pages);
		next_posts_link('Next>>', $posts->max_num_pages);

	或 
        echo paginate_links( array(
            'total' => $posts->max_num_pages
        ));
```

### wp_trim_words
```php
本函数会删除掉 img
                                    <?php echo wp_trim_words( get_the_content(), 150 ); ?>
```



