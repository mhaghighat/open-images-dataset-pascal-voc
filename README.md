# Open Images Dataset + VOC PASCAL annotations

Open Images is a dataset of ~9 million URLs to images that have been annotated with labels spanning over 6000 categories. 

This page aims to provide the download instructions for OpenImages V4 and it's annotations in VOC PASCAL format. 

Please visit the [project page](https://storage.googleapis.com/openimages/web/index.html) for more details on the dataset .

## The Problem this repository is trying to resolve

Open Images annotation file comes in the following format:

```
ImageID,Source,LabelName,Confidence,XMin,XMax,YMin,YMax,IsOccluded,IsTruncated,IsGroupOf,IsDepiction,IsInside
000026e7ee790996,freeform,/m/07j7r,1,0.071905,0.145346,0.206591,0.391306,0,1,1,0,0
000026e7ee790996,freeform,/m/07j7r,1,0.439756,0.572466,0.264153,0.435122,0,1,1,0,0
000026e7ee790996,freeform,/m/07j7r,1,0.668455,1.000000,0.000000,0.552825,0,1,1,0,0
000062a39995e348,freeform,/m/015p6,1,0.205719,0.849912,0.154144,1.000000,0,0,0,0,0
000062a39995e348,freeform,/m/05s2s,1,0.137133,0.377634,0.000000,0.884185,1,1,0,0,0
0000c64e1253d68f,freeform,/m/07yv9,1,0.000000,0.973850,0.000000,0.043342,0,1,1,0,0
0000c64e1253d68f,freeform,/m/0k4j,1,0.000000,0.513534,0.321356,0.689661,0,1,0,0,0
0000c64e1253d68f,freeform,/m/0k4j,1,0.016515,0.268228,0.299368,0.462906,1,0,0,0,0
0000c64e1253d68f,freeform,/m/0k4j,1,0.481498,0.904376,0.232029,0.489017,1,0,0,0,0
```

XMin, XMax, YMin, YMax are the coordinates of the bounding box, in normalized image coordinates. XMin is in [0,1], where 0 is the leftmost pixel, and 1 is the rightmost pixel in the image. Y coordinates go from the top pixel (0) to the bottom pixel (1).

This particular format presents a challenge when converting to VOC PASCAL xml since bounding box in VOC is represented by pixel value. No particular file contains the actual dimensions of the images, so conversion will require to download all images, register it's width and height and then executing the conversion of the normalized values into pixel positions.

Considering that the Open Images Dataset is massive, this can take a while. Therefore making more difficult to use Open Images dataset in frameworks that expect VOC or COCO format.

# Download Images and VOC PASCAL annotations

## Download Images that have Bounding Boxes Annotations

CVDF hosts image files that have bounding boxes annotations in the Open Images Dataset V4. The images are split into train (1,743,042), validation (41,620), and test (125,436) sets. The train set is also used in the Open Images Challenge 2018.

The images are rescaled to have at most 1024 pixels on their longest side, while preserving their original aspect-ratio. The total size is 561GB. The images can be directly downloaded into a local directory from the CVDF AWS S3 cloud storage bucket:

```
s3://open-images-dataset
```

You can either download the images to a local directory or to your own AWS S3 cloud storage bucket with the following procedures:
1. install [awscli](https://aws.amazon.com/cli/)
2. download images for the train set, validation set, test set:
  * aws s3 --no-sign-request sync s3://open-images-dataset/train [target_dir/train] (513GB)  
  * aws s3 --no-sign-request sync s3://open-images-dataset/validation [target_dir/validation] (12GB)  
  * aws s3 --no-sign-request sync s3://open-images-dataset/test [target_dir/test] (36GB)


The target_dir can be a local directory or a AWS S3 cloud storage bucket.

(all credit to CVDF who are hosting the files https://github.com/cvdfoundation/open-images-dataset )

## Download VOC annotation data

Please keep in mind that this VOC annotation data will only work with the rescaled images produced by CVDF. If rescaling is different from CVDF hosted version, the scale for the bounding boxes will be wrong.

  * wget http://open_images_voc.datoscientific.com/open_imagesv4_train_annotations.tar.gz (209MB compressed, 6.9GB uncompressed, 1,743,042 xml files) 
  * wget http://open_images_voc.datoscientific.com/open_imagesv4_val_annotations.tar.gz (4.9MB compressed, 165MB uncompressed, 41,620 xml files)  
  * wget http://open_images_voc.datoscientific.com/open_imagesv4_test_annotations.tar.gz (15MB compressed, 499MB uncompressed, 125,436 xml files)  

# How I generated this data

The following files have been generated by:

1. Translating all MID formated classed described in the annotations into it's short description using https://storage.googleapis.com/openimages/2018_04/class-descriptions.csv



```
/m/0pc9,Alphorn
/m/0pckp,Robin
/m/0pcm_,Larch
/m/0pcq81q,Soccer player
/m/0pcr,Alpaca
/m/0pcvyk2,Nem
/m/0pd7,Army
/m/0pdnd2t,Bengal clockvine
/m/0pdnpc9,Bushwacker
/m/0pdnsdx,Enduro
/m/0pdnymj,Gekkonidae
```

2. Grouping all annotations by image and saving every annotation related to the image into a corresponding xml file named identically to the imageID
