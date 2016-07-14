WP GeoMeta
===========
A WordPressy spatial foundation for WordPress.

This is a plugin and a library for other plugins to use. It makes
working with spatial data pretty much the same as working with 
any other metadata.


Usage
-----

### Writing and Reading Data
Store GeoJSON strings as metadata like you would for any other metadata. 

Add geometry to a post:

    $single_feature = '{ "type": "Feature", "geometry": {"type": "Point", "coordinates": [102.0, 0.5]}, "properties": {"prop0": "value0"} }';
    add_post_meta(15,'singlegeom',$single_feature,false);

Update the post geometry: 	

    $single_feature = '{ "type": "Feature", "geometry": {"type": "Point", "coordinates": [-93.5, 45]}, "properties": {"prop0": "value0"} }';
    update_post_meta(15,'singlegeom',$single_feature,false);

Read GeoJSON back from the post;

    $single_feature = get_post_meta(15, 'singlegeom'); 
	print $singlegeom;
	// '{ "type": "Feature", "geometry": {"type": "Point", "coordinates": [-93.5, 45]}, "properties": {"prop0": "value0"} }';

### Querying

Querying is done through the WP_Query meta_query argument. Set _compare_ to a [supported spatial operator](https://wordpress.org/plugins/wp-spatial-capabilities-check/) for your system. The _value_ should be GeoJSON representing the second geometry to compare with. 

Query posts spatially and print their titles:

    $q = new WP_Query( array(
    	'meta_query' => array(
    		array(
    			'key' => 'singlegeom',
    			'compare' => 'ST_INTERSECTS',
    			'value' => '{"type":"Feature","geometry":{"type":"Point","coordinates":[-93.5,45]}}',
    		)
    	)
    ));
    
    while($q->have_posts() ) {
    	$q->the_post();
    	print "\t* " . get_the_title() . "\n";
    }


Server Requirements
-------------------

### WordPress
Probably WordPress 4.4 since we are using the meta_query parameters of get_terms 
which was introduced in that version.

### MySQL
MySQL 5.6.1 or higher is strongly recommended. 

WP_GeoMeta will probably work on MySQL 5.4, but spatial support was pretty weak 
before version 5.6.1. 

Before MySQL 5.6.1 spatial functions worked against the mininum bounding rectangle 
instead of the actual geometry.

MySQL 5.7 brough spatial indexes to InnoDB tables. Before that only MyISAM tables
supported spatial indexes. Anything else required a full table scan. 

If you are using MySQL 5.7, good for you, and consider converting your geo tables
to InnoDB! (and let me know how it goes).

### PHP
The goal is PHP 5.2 support, just like WordPress's minimum version, even though it's
ancient. 

The Problems
------------
### GIS + WordPress = ???
GIS is awesome (and popular!). WordPress is awesome (and popular!). Unfortunately
they don't interact very much. If you drew a ven diagram, the two circles would
just barely be touching. 

And most of that touching is just embedded maps from 3rd party services.

GIS and WordPress should become better friends.

### GIS + Developers = ???
GIS isn't hard, but it's different. WordPress developers don't need or want to 
learn yet another thing for just one project. WordPress admins have even less 
desire to dig into the murky details of GIS.

WordPress isn't hard, but it's different. GIS developers don't need or want to
learn yet another thing for just one project. GIS admins have even less desire 
to dig into the murky details of WordPress.


Next Todos
----------
 * Support single geometry compairson operators.
 * Support spatial orderby
 * Live examples

Future Enhancements
-------------------
 * Where do errors go / who sees them? Eg. inside added_meta callback
 * Buffering is a very common operation, but it doesn't work well in EPSG:4326. 
 * Add filter to let users/devs explicitly define meta keys to filter on w/constant to enable the filter
 * Lat/Lng migration tool or plugin that detects coord pairs
 * Add callbacks/hooks so that other plugins with custom tables (eg. Gravity Forms) could
store geo data in a geo way.

Changes
-------

### 0.1.0: Perfect Tommy
 * Will now work as a library or a plugin. 
 * Additional functions for getting data back into GeoJSON format.
 * Working well enough to use in production.


### 0.0.2: New Jersey
 * Improved meta query capabilities. Now support sub queries, and uses standard meta-query syntax
 * Whitelist of known spatial functions in meta_query args. Allowed args set by detecting MySQL capabilities.
 * We now delete the spatial index on activation so that we don't end up with duplicate spatial keys
 * Populate geo tables on activation with any existing geojson values
 * Submitted ticket to dbDelta SPATIAL INDEX support: https://core.trac.wordpress.org/ticket/36948
 * Conform to WP coding standards
 * Explicitly set visibility on properties and methods

### 0.0.1: Emilio Lizardo
 * Initial Release

Quotes
-----
 * "The ACF of Geo Queries" -- Nick
 * "No matter where you go, there you are"
