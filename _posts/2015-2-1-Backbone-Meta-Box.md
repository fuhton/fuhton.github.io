---
layout: post
title: Add a new Meta Box for a BackboneJS Applciation
---

Adding a meta box to your WordPress admin for use in a BackboneJS application is very simple. With a single call you can create the UI for the meta box.
```
<?php
//define namespace
namespace Test\Test;

/**
 * Load various actions for this post meta
 *
 * @return void.
 */
function load() {
  add_action( 'add_meta_boxes', __NAMESPACE__ . '\store' );
}

load();
```

Above we define the namespace and a load function for sourcing in our meta boxes. We don’t need to define a show function for our meta box, because that will be defined by our ‘store’ function below. The store function utilizes WordPress’s built in ‘add_meta_box’ function to create the meta box. While separating out the ‘$type’ variable isn’t 100% necessary, it helps us objectively get a broader view of our post types that will utilize this meta box.
```
/**
 * Create Meta boxes for the meta cpt
 * @since 0.1.0
 *
 * @return void.
 */
function store() {
  //Define the post types
  $types = array( 'post' );

  foreach( $types as $type ) {
    add_meta_box(
       'episode_information',
       __('Episode Information', 'Test'),
       __NAMESPACE__ . '\show',
       $type,
       'normal',
       'high'
    );
  }
}
```

After we’ve created the meta box, we need a way to display the meta box and it’s associated information. Our ‘show’ function, as called in our store function, will do all this leg work. Now for a normal meta box, there would be forms displayed that will hook directly into an ‘update’ function, but because we’re creating an BackboneJS AJAX application we don’t need to worry about that. For AJAX security reasons, we include a callout to wp_nonce_field to create a security object. Lastly we include an HTML Div object to hook our Backbone Application upon.
```
/**
 * Callback function meta box creation
 *
 * @since 0.1.0
 *
 * @param object $post post object
 * @param array $metabox post object
 *
 */

function show( $post, $metabox ) {
  wp_nonce_field( 'test_nonce', 'test_nonce' );
  ?>
    <div id="test_meta_box"></div>
  <?php
}
```
And that’s all we need to get started from WordPress.
