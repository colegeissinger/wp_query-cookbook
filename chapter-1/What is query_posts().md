# What is query_posts()
Query_posts() is a wrapper function of WP_Query()
Replaces/alters the main query after it is ran
simplistic, and problematic
breaks pagination
re-runs SQL queries, resulting in increased page load times, sometimes doubling the amount of work needed.
Will completely fail in some circumstances
pre_get_posts() is the preferred method for modifying the main query.
Cannot support multiple queries
If used in a page template, WordPress has already executed the query for that current page before your code in the page template is even ran. So when your query_posts() is ran, you over-ride the default query that was ran before page load with a new query, adding more database calls.

query_posts() creates a new WP_Query() which then modifies the global variable $wp_query. Returns and array of posts. Fetched with global the_post() function and is passed to setup_postdata() which is then handed to the global variable $post. To reset our global variable $wp_query we use wp_reset_query().

    function &query_posts($query) {
        unset($GLOBALS['wp_query']);
        $GLOBALS['wp_query'] = new WP_Query();
        return $GLOBALS['wp_query']->query($query);
    }

If we use query_posts() to add a featured post to the homepage, a lot happens before WordPress gets to our code to query for that post.

* A URL is passed to WordPress
* WordPress core files are loaded
* Activated plugins are loaded
* Internationalization files are loaded and the functions.php in the active theme
* URL is parsed into a query specification
* Set all the is_ variables that are used in conditional tags
* Database is queried and saved into $wp_query
* 404 errors are handled
* The HTTP headers are sent
* Some final variables are set for the loop
* Template page is loaded

#### Main Loop
    $wp_the_query = new WP_Query();
    $wp_query = $wp_the_query();
The main loop consists of 3 main queries
$wp_the_query (the default main query, but should never be called or used directly!)
$wp_query (a live copy of $wp_the_query and is setup for our use/modification)
$post (the actual post data)

query_posts() looks to $wp_query but never to $wp_the_query. This is problematic because what if $wp_query is modified by another loop and it doesn't get reset... Your now querying the wrong post data! D'oh!

Notes: 	http://developer.wordpress.com/2012/05/14/querying-posts-without-query_posts/
		http://wordpress.stackexchange.com/questions/1753/when-should-you-use-wp-query-vs-query-posts-vs-get-posts
