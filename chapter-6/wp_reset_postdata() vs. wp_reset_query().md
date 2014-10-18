# wp_reset_postdata() vs. wp_reset_query()
##### wp_reset_postdata()
every new WP_Query() replaces the $post global with the post data of your loop you just ran. If you don't reset it, you might end up with $post data from your WP_Query() in your main loop.