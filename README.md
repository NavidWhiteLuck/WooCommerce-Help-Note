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

# Remove all products 
🌎 The best and fastest way to delete all products from WooCommerce is by directly interacting with the MySQL database. By using SQL queries, you can efficiently remove all product-related data, including product posts, metadata, and associated taxonomies, without having to rely on the WooCommerce admin panel, which may be slower or limited for large datasets. The SQL commands directly target the relevant tables (wp_posts, wp_postmeta, wp_terms, wp_term_taxonomy, and wp_term_relationships) ensuring a comprehensive cleanup of product data.

بهترین و سریع‌ترین راه برای حذف تمامی محصولات از ووکامرس، تعامل مستقیم با دیتابیس MySQL است. با استفاده از کوئری‌های SQL می‌توانید به‌صورت کارآمد تمام داده‌های مرتبط با محصولات، از جمله پست‌های محصول، متادیتا و طبقه‌بندی‌های مرتبط را حذف کنید، بدون نیاز به استفاده از پنل ادمین ووکامرس که ممکن است برای مجموعه داده‌های بزرگ کند یا محدود باشد. این دستورات SQL مستقیماً جداول مرتبط (مثل wp_posts، wp_postmeta، wp_terms، wp_term_taxonomy و wp_term_relationships) را هدف قرار می‌دهند تا تمامی داده‌های محصول به‌طور کامل پاکسازی شود.

```
-- بخض اول
DELETE relations.*, taxes.*, terms.*
FROM wp_term_relationships AS relations
INNER JOIN wp_term_taxonomy AS taxes
    ON relations.term_taxonomy_id = taxes.term_taxonomy_id
INNER JOIN wp_terms AS terms
    ON taxes.term_id = terms.term_id
WHERE object_id IN (
    SELECT ID FROM wp_posts WHERE post_type IN ('product', 'product_variation')
);

-- بخش دوم
DELETE FROM wp_postmeta
WHERE post_id IN (
    SELECT ID FROM wp_posts WHERE post_type IN ('product', 'product_variation')
);

-- بخض سوم
DELETE FROM wp_posts
WHERE post_type IN ('product', 'product_variation');
```

# توضیحات:
بخش اول: حذف تمامی روابط (terms, taxonomies) مرتبط با محصولات و تغییرات محصولات.
بخش دوم: حذف تمامی متادیتاهای مرتبط با محصولات و تغییرات محصولات.
بخش سوم: حذف تمامی پست‌های مربوط به محصولات و تغییرات محصولات.