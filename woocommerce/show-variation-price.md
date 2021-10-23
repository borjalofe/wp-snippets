# Show Variation Price

I developed this snippet for WooCommerc-based websites that use product variations and want to show the price of the specific variation the user is selecting instead of the price range shown by default.

## Table of Contents

- [Show Variation Price](#show-variation-price)
  - [Table of Contents](#table-of-contents)
  - [Status](#status)
  - [Use this code](#use-this-code)

## Status

The snippet works fine with WordPress 5.8.

## Use this code

```php
add_action('woocommerce_before_add_to_cart_form', 'selected_variation_price_replace_variable_price_range');
function selected_variation_price_replace_variable_price_range(){
    global $product;

    if( $product->is_type('variable') ):
    ?>
    <script id="modificar-precio" type="text/javascript">
        window.onload = (e) => {
            let labels = document.querySelectorAll('.variations label');
            let params = [];
            let variations = JSON.parse(document.querySelector('form.variations_form.cart').getAttribute('data-product_variations'));

            labels.forEach((element) => {
                let id = element.htmlFor;
                let label = 'attribute_'+id;
                params.push([label, '']);
                document.getElementById(id).addEventListener('change',(e) => {
                    let select = e.target;
                    let key = 'attribute_' + select.id;
                    params.forEach((element) => {
                        if (element[0] === key) {
                            element[1] = select.options[select.options.selectedIndex].value;
                        }
                    });
                    variations.forEach((variation) => {
                        let matches = 0;
                        params.forEach((param) => {
                            if (variation.attributes[param[0]] === param[1]) {
                                matches++;
                            }
                        })
                        
                        if (matches === params.length) {
                            let price = variation.display_price;
                            let priceText = '<span class="woocommerce-Price-amount amount"><bdi>'+price+',00<span class="woocommerce-Price-currencySymbol">€</span></bdi></span>';
                            document.querySelector('p.price').innerHTML = priceText;
                        }
                    });
                });
            });
        }
    </script>
<?php
    endif;
}
```
