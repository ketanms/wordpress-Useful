1.Change site url Query in Database 

UPDATE wp_options SET option_value = replace(option_value, 'http://www.oldurl', 'http://www.newurl') WHERE option_name = 'home' OR option_name = 'siteurl';

UPDATE wp_posts SET guid = replace(guid, 'http://www.oldurl','http://www.newurl');

UPDATE wp_posts SET post_content = replace(post_content, 'http://www.oldurl', 'http://www.newurl');

UPDATE wp_postmeta SET meta_value = replace(meta_value,'http://www.oldurl','http://www.newurl');

============================================================================================================
2.Two post array merge

$all_post = new WP_Query(array(
				'post_type'      => array('post','press_release','press_coverage','videos','downloads'),
				'posts_per_page' => -1,
				'post_status'    => 'publish',
				's'				 => $_GET['q'],
				'order'          => 'DESC',
				'fields'         => 'ids'
			 ));
			
		  $spl_result = new WP_Query(array('post_type'      => array('page'),
							'posts_per_page' => -1,
							'post_parent__in' => array(2483, 171, 22, 169), 
							'post_status'    => 'publish',
							's'				 => $_GET['q'],
							'order'          => 'DESC',
							'fields'		=> 'ids' ));
		$result_arr = array_merge( $all_post->posts, $spl_result->posts );

		 query_posts(array(
		 				'post_type'      => array('post','press_release','press_coverage','videos','page','downloads'),
		 				'post__in' => $result_arr,
						'posts_per_page' => 9,
						'paged'          => $paged,
					));
		 

	}

=================================================================================================

3.Meta Query
$data_ids = new WP_Query(array(
			             'post_type'      => 'downloads',
                 		 'posts_per_page' => -1,
			           	 'paged'          => $paged,
                 		 'post_status'    => 'publish',
                 		 'order'          => 'DESC',
				         'fields'		=> 'ids',
				         'meta_query' => array(
									array(
                          'key' => 'display_customer_area',
                          'value'=> 'display-customer-area',
      									'type' => 'CHAR',
      									'compare'=> 'LIKE'									
                      )
								)
			));
	if ( have_posts() ) : ?>
    <?php while ( have_posts() ) : the_post(); ?>

  	<?php endwhile;?>
    <?php endif;?>

=====================================================================================================

4.Breadcrumb NavXT Plugin
<?php
  if ( !(is_front_page()) ) { ?>

<div class="breadcrumbs" typeof="BreadcrumbList" vocab="http://schema.org/">
    <?php if(function_exists('bcn_display'))
    {
        bcn_display();
    }?>
</div>
<?php } ?>

-------------------------------------------------------------------------------------------------------
5.Accordion Section

<div class="container">
    <div class="row">
        <div class="col-xs-12 col-sm-12 col-md-12 col-lg-12">

           <?php $faq_accordion = get_field('treatment_accordion', $post->ID);
           if(!empty($faq_accordion)){?>
            <h2 class="faqs-rel-treatment-title">FAQ's</h2>

            <?php 
            if(!empty($faq_accordion)) {  ?>
             <div class="treatment-accordion-secetion">
           <?php foreach ( $faq_accordion as $faq_acco ) {?>
             <div class="maccordion">
              <div class="accordion_head1">
                
                <div class="accordion_head plusminus-sign">+</div>
                <div class="accordion-title"><?php echo $faq_acco['accordion_title']; ?>
                </div>
                
              </div>
              <div class="accordion_body1" style="display: none;">
                <div class="accordion-description"> <?php echo $faq_acco['accordion_description']; ?></div>
              </div>
             </div>
              <?php } ?>
             </div>
            <?php } ?>  
            <?php } ?>
        </div>
    </div>
</div>


<script>
jQuery(document).ready(function ($) {
    //toggle the component with class accordion_body
    $(".accordion_head1").click(function () {
        if ($('.accordion_body1').is(':visible')) {
            $(".accordion_body1").slideUp(300);
            $(".plusminus-sign").text('+');
        }
        if ($(this).next(".accordion_body1").is(':visible')) {
            $(this).next(".accordion_body1").slideUp(300);
            $(this).children(".plusminus-sign").text('+');
        } else {
            $(this).next(".accordion_body1").slideDown(300);
            $(this).children(".plusminus-sign").text('-');
        }
    });
});
</script>
-------------------------------------------------------------------------------------------------------

6.Sticky Header

Main header : s_header
Sticky menu :<nav class="navbar navbar-inverse animated fadeIn navbar-fixed-top">

<script>
      jQuery(document).ready(function($) {
        $(window).scroll(function() {
        var y = $(window).scrollTop(); 
        var hh = $('.s_header').outerHeight();
        var ww = $(window).width();
         if(y > hh)
         {
          $('.navbar').removeClass("animated fadeIn navbar-fixed-top");
          $('.navbar').addClass("animated fadeIn navbar-fixed-top");
         }
         else{
          $('.navbar').removeClass("animated fadeIn navbar-fixed-top");
         }
         });
        });
</script>

-------------------------------------------------------------------------------------------------------
7.LATEST NEWS

<div class="row">
            <h2 class="latestnews-section-title">Latest News</h2>
                    <?php
                  $args = array(
                  'post_type'=> 'post',
                  'posts_per_page'=> 3,
                  'post_status'    => 'publish',
                  'order'=>'DESC',
                );
                  $posts_array = get_posts( $args ); ?>

                        <?php foreach ( $posts_array as $post ) : setup_postdata( $post ); ?>
                       <a href="<?php echo the_permalink();?>"><?php echo the_title(); ?></a>
                          <div class="latestnews-post-text">
                              <?php
                                $excerpt= get_the_excerpt(); 
                                echo substr($excerpt, 0, 100); 
                              ?>
                          </div>
                          <div class="latestnews-post-date"><i class="fa fa-calendar" aria-hidden="true"></i><?php 
                          $post_date = get_the_date( 'jS F Y' ); 
                          echo $post_date;
                          ?></div>
                          <div class="latestnews-readmore"><a href="<?php echo the_permalink();?>" class="btn btn-primary read-more-btn"><i class="fa fa-arrow-circle-o-right" aria-hidden="true"></i><span>Read more</span></a>  </div>
                        
                       <?php endforeach; 
                       wp_reset_postdata();?>
 </div> 

-------------------------------------------------------------------------------------------------------
8.Video Tag

<?php
$args = array(
			'post_type'      => 'videos',
			'posts_per_page' => -1,
			'post_status'    => 'publish',
			'order'          => 'DESC',
			'fields'         => 'ids',
			'meta_query' => array(									
				array(
				'key' => 'display_customer_area',
				'value'=> 'display-customer-area',
				'type' => 'CHAR',
				'compare'=> 'LIKE'									
				)				
		));
		
		$video_posts = get_posts($args);
		$vcategories = array();
		foreach( $video_posts as $videoid ){
			$args = array("fields" => "ids");
			$video_cat = wp_get_post_terms( $videoid, 'vd_tags', $args );
			$vcategories = array_merge( $vcategories, $video_cat );
		}
		$vcategory = array_unique($vcategories);
		?>
        <ul>
        <?php
		
		foreach($vcategory as $vcat)
		{		
 			$terms = get_term( $vcat, 'vd_tags'); 
			//print_r($terms);
		?>	
			<li>
            	<a href="<?php echo get_term_link($terms->term_id); ?>"><?php echo $terms->name; ?></a>
            </li>
        <?php     
		}
        ?>
		</ul>


-------------------------------------------------------------------------------------------------------
9.Event Date:

<?php
$today = date('Y-m-d');
		  query_posts(array(
				'post_type'      => 'events',
				'posts_per_page' => 1,
				'post_status'    => 'publish',
				'meta_key'		 => 'event_date',
				'orderby'		 => 'meta_value',				
				'order'          => 'DESC',
				'meta_query'	=> array(
					'relation'		=> 'AND',
					array(
						'key'	 	=> 'event_date',						
						'compare' 	=> '>',
						'value'	  	=> $today,
						'type'		=> 'DATE',
					),
				),	
			));
			?>
			<?php	if ( have_posts() ) : 
         while ( have_posts() ) : the_post(); ?>
<?php 
		$edate = strtotime(get_field('event_date'));
  		if($edate) { 
			echo date('F jS,Y',$edate); 
		}
	?>
			<?php endwhile; 
 				endif; ?>

-------------------------------------------------------------------------------------------------------
=======================================================================================================


1.Convert html into wordpress : https://htmltowordpress.io/

============================================================================================================

2.WP loop:
<?php $args = array('posts_per_page'   => '',
                             'post_status'      => 'publish',
                             'post_type'        => '',
                       'order'            => 'DESC' );
      $posts_array = get_posts( $args ); 
       foreach ( $posts_array as $post ) : setup_postdata( $post ); ?>
      <?php endforeach; 
        wp_reset_postdata();?>


<?php 
============================================================================================================

3.
/**/
Create WP Themes 
http://underscores.me/
https://github.com/devinsays/options-framework-theme

/*Custom post*/
https://css-tricks.com/creating-meet-team-page-wordpress/
https://css-tricks.com
-------
https://generatewp.com/post-type/
Post Type Generator
============================================================================================================

4.
/*Add Shortcode*/
add_shortcode( 'home_banner_date_and_cash', 'home_banner_date_cash' );

function home_banner_date_cash( $atts ){ 
  
 ob_start();
 global $cash;
 $cash = $atts['cash'];
 get_template_part( 'inc/inc-home-banner-date-cash' );
 
 return ob_get_clean(); 
 
}

/*SHortcode-2*/
function bartag_func( $atts ) {
  $atts = shortcode_atts(
    array(
      'foo' => 'no foo',
      'bar' => 'default bar',
    ), $atts, 'bartag' );

  return 'bartag: ' . $atts['foo'] . ' ' . $atts['bar'];
}
add_shortcode( 'bartag', 'bartag_func' );

o/p:[bartag foo="koala" bar="bears"]

============================================================================================================

5.Not display UPDATE plugin :
define('DISALLOW_FILE_MODS',true);

============================================================================================================

6.Display Random Header Images in WordPress

    headerimage_1.gif
    headerimage_2.gif
    headerimage_3.gif

<img src="http://path_to_images/headerimage_<?php echo(rand(1,3)); ?>.gif" width="image_width" height="image_height" alt="image_alt_text" />

============================================================================================================

7.Sroll page:
<script type="text/javascript">
    jQuery(document).ready(function($){
      $(".req").click(function() {
        $('html,body').animate({
        scrollTop: $(".callback").offset().top-180},
        2000);
        
      });
    });
  </script>
