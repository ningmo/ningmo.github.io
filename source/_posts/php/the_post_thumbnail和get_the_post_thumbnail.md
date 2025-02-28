---
title: the_post_thumbnail和get_the_post_thumbnail
categories: [php]
date: 2012-12-18 23:01:30
---

<div>the_post_thumbnail 在 WordPress 中主要用来打印文章中设定的缩略图，而 get_the_post_thumbnail 函数可以将你需要的 HTML 代码以字符串的形式返回。</div>
<div><strong>the_post_thumbnail</strong> 函数的使用
<div>the_post_thumbnail($size,$attr)</div>
函数参数$size 是指你想要的缩略图类型 默认是 ‘post-thumbnail’ 也就是特色图像
$attr 图像img标签中的属性设置。

<strong>the_post_thumbnail</strong> 函数声明
<div>/**  * Display Post Thumbnail.  *  * @since 2.9.0  *  * @param int $size Optional. Image size. Defaults to 'post-thumbnail', which theme sets using set_post_thumbnail_size( $width, $height, $crop_flag );.  * @param string|array $attr Optional. Query string or array of attributes.  */functionthe_post_thumbnail($size='post-thumbnail',$attr=''){echoget_the_post_thumbnail(null,$size,$attr);}</div>
<strong>get_the_post_thumbnail</strong> 函数声明
<div>*Retrieve Post Thumbnail.**@since 2.9.0  **@param int $post_idOptional.Post ID.*@param string $sizeOptional.Image size.Defaults to 'post-thumbnail'.*@param string|array$attrOptional.Query string or arrayof attributes.*/functionget_the_post_thumbnail($post_id=null,$size='post-thumbnail',$attr=''){$post_id=(null===$post_id)? get_the_ID():$post_id;$post_thumbnail_id=get_post_thumbnail_id($post_id);$size=apply_filters('post_thumbnail_size',$size);if($post_thumbnail_id){do_action('begin_fetch_post_thumbnail_html',$post_id,$post_thumbnail_id,$size);// for "Just In Time" filtering of all of wp_get_attachment_image()'s filtersif(in_the_loop())update_post_thumbnail_cache();$html=wp_get_attachment_image($post_thumbnail_id,$size,false,$attr);do_action('end_fetch_post_thumbnail_html',$post_id,$post_thumbnail_id,$size);}else{$html='';}returnapply_filters('post_thumbnail_html',$html,$post_id,$post_thumbnail_id,$size,$attr);}</div>
</div>
