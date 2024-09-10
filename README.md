# WooCommerce-Help-Note
This repository helps you to handle WooCommerce tasks in WordPress more easily.

## Remove product's attributes
🌎 WooCommerce doesnt offer a built-in option for bulk deleting product attributes. However, if you'd like to remove all of your product attributes at once, you can execute the following SQL query directly within PHPMyAdmin:

ووکامرس گزینه‌ای داخلی برای حذف دسته‌جمعی ویژگی‌های محصول ارائه نمی‌دهد. با این حال، اگر می‌خواهید تمامی ویژگی‌های محصولات خود را به‌صورت یکجا حذف کنید، می‌توانید کوئری SQL زیر را مستقیماً در PHPMyAdmin اجرا کنید."


```
DELETE FROM wp_terms 
WHERE term_id IN (
    SELECT term_id 
    FROM wp_term_taxonomy 
    WHERE taxonomy LIKE 'pa_%'
);

DELETE FROM wp_term_taxonomy 
WHERE taxonomy LIKE 'pa_%';

DELETE FROM wp_term_relationships 
WHERE term_taxonomy_id NOT IN (
    SELECT term_taxonomy_id 
    FROM wp_term_taxonomy
);

DELETE FROM wp_woocommerce_attribute_taxonomies;

DELETE FROM wp_termmeta 
WHERE meta_key LIKE 'order_pa_%';

DELETE FROM wp_options 
WHERE option_name LIKE '_transient_wc_%'; 
```