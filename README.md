# Wordpress functions.php codes to make your life easier
A list of useful functions.php codes for wordpress

### Changes VAT to GST
```
add_filter( 'gettext', function( $translation, $text, $domain ) {
if ( $domain == 'woocommerce' ) {
if ( $text == '(ex. VAT)' ) { $translation = '(ex. GST)'; }
}
return $translation;
}, 10, 3 );
```

### Refresh Checkout price box when payment method is selected

```
add_action('wp_footer', 'minicart_checkout_refresh_script');
function minicart_checkout_refresh_script(){
    if ( is_checkout() && ! is_wc_endpoint_url() ) :
    ?>
    <script type="text/javascript">
    (function($){
        $(document.body).on('change', 'input[name="payment_method"],input[name^="shipping_method"]', function(){
            $(document.body).trigger('update_checkout').trigger('wc_fragment_refresh');
        });
    })(jQuery);
    </script>
    <?php
    endif;
}
```

### Show discounted amount instead of regular price on checkout page

```
add_filter('woocommerce_coupon_get_discount_amount', 'woocommerce_discount_from_the_original_price', 10, 5 );
function woocommerce_discount_from_the_original_price( $discount, $discounting_amount, $cart_item, $single, $coupon ) {

  if ($coupon->discount_type == 'percent' && $coupon->code == 'staff502019') {

    $discount_percentage = $coupon->amount / 100;
    $item                = wc_get_product($cart_item['product_id']);

    if ($item) {
      //
      // example product is £100 down to £66.66
      // example discount is 50%
      //
      if ( $item->is_type( 'simple' ) ) {
        $sale_price    = $item->sale_price;
        $regular_price = $item->regular_price;
        //echo '<p>Simple | Regualr Price: £' . $regular_price . ' | Sale Price: £' . $sale_price . '</p>';
        if ( ($sale_price && $regular_price) && ($sale_price !==  $regular_price) ) {
          $discount_from_regular_price = $regular_price * $discount_percentage; // e.g. £100 * 0.5 = £50
          $discount = $discounting_amount - ($discount_from_regular_price * $cart_item['quantity']); // e.g. £66.66 - (£50 x 1) = £16.66
        }

      } elseif ( $item->is_type( 'variable' ) ) {
        $variable_product = new WC_Product_Variation( $cart_item["variation_id"] );
        $sale_price    = $variable_product->sale_price;
        $regular_price = $variable_product->regular_price;
        //echo '<p>Variable | Regualr Price: £' . $regular_price . ' | Sale Price: £' . $sale_price . '</p>';
        if ( ($sale_price && $regular_price) && ($sale_price !==  $regular_price) ) {
          $discount_from_regular_price = $regular_price * $discount_percentage;
          $discount = $discounting_amount - ($discount_from_regular_price * $cart_item['quantity']);
        }

      }
    }

  }
  return $discount;
}
```

### Elementor Phone / Telephone number Validation

```
// Elementor Form Telephone Number Validition
//======================================
add_action( 'elementor_pro/forms/validation/tel', function( $field, $record, $ajax_handler ) {
	// Match this format XXXXXXXXXX, 1234567890
	if ( preg_match( '/[0-9]{10}/', $field['value'] ) !== 1 ) {
	$ajax_handler->add_error( $field['id'], 'Mobile Number Should be of 10 Digits' );
	}
 	}, 10, 3 );
```

### Move Mobile Number Input To Top on checkout page
This helps in abandon cart picking data quickly.

```
add_filter( 'woocommerce_checkout_fields', 'move_checkout_fields' );

function move_checkout_fields( $fields ) {
    $fields['billing']['billing_email']['priority'] = 1;
    $fields['billing']['billing_phone']['priority'] = 2;
    return $fields;
}
```

### Add Custom Text below Blog single post

```
function display_custom_text_after_post_content( $content ) {
    if ( is_singular('post') && ! is_admin() && ! is_feed() ) {
        $custom_text = 'Add your custom text here'; 

        $content .= $custom_text;
    }

    return $content;
}
add_filter( 'the_content', 'display_custom_text_after_post_content' );

```
