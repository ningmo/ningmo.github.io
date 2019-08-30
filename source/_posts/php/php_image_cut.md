---
title: php截图裁剪 缩放图片
date: 2016-10-25 20:49:04
tags: [image,php]
categories: php
---

```
function image_resize($from, $tw,$th){
        // 按指定大小生成缩略图，而且不变形，缩略图函数
        $temp = array(1=>'gif', 2=>'jpeg', 3=>'png');
        list($fw, $fh, $tmp) = getimagesize($from);
        if(!$temp[$tmp]){
            return false;
        }
        $tmp = $temp[$tmp];
        $infunc = "imagecreatefrom$tmp";
        $outfunc = "image$tmp";
        $fimg = $infunc($from);
        // 使缩略后的图片不变形，并且限制在 缩略图宽高范围内
        if($fw/$tw > $fh/$th){
            $th = $tw*($fh/$fw);
        }else{
            $tw = $th*($fw/$fh);
        }
        $timg = imagecreatetruecolor($tw, $th);
        imagecopyresampled($timg, $fimg, 0,0, 0,0, $tw,$th, $fw,$fh);
        $to = C('UPLOAD_PATH').C('UPLOAD_SAVE_DIR_RULE').'a_'.time().'_'.mt_rand(100,999).'.'.end(explode('.',$from));
        $r = $outfunc($timg, $to);
        if($r){
            unlink($from);
            return $to;
        }else{
            return false;
        }
    }
    function cut_img($from,$zero=[0,0],$cutSize=[300,300]){
        $temp = array(1=>'gif', 2=>'jpeg', 3=>'png');
        list($fw, $fh, $tmp) = getimagesize($from);
        if(!$temp[$tmp]){
            return false;
        }
        $tmp = $temp[$tmp];
        $infunc = "imagecreatefrom$tmp";
        $outfunc = "image$tmp";
        $timg = $infunc($from);
        $cutImg=imagecreatetruecolor($cutSize[0], $cutSize[1]);
        # 将 $timg 图像中坐标从 src_x，src_y  开始，宽度为 src_w，高度为 src_h 的一部分拷贝到 dst_im 图像中坐标为 dst_x 和 dst_y 的位置上。
        imagecopy($cutImg,$timg,0,0,$zero[0], $zero[1],$cutSize[0], $cutSize[1]);
        $to = C('UPLOAD_PATH').C('UPLOAD_SAVE_DIR_RULE').'a_'.time().'_'.mt_rand(100,999).'.'.end(explode('.',$from));
        $r = $outfunc($cutImg, $to);
        if($r){
            unlink($from);
            return str_replace('../','/',$to);
        }else{
            return false;
        }
    }
```