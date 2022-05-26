:exclamation::exclamation: **This repo will no longer be maintained, please visit https://github.com/milvus-io/bootcamp** :exclamation: :exclamation:

# Reverse Image Search Based on Milvus and VGG

This demo uses VGG, an image feature extraction model, and Milvus to build a system that can perform reverse image search.

Below is the system architecture:

<img src="pic/demo.jpg" width = "450" height = "600" alt="system_arch" align=center />

### Environment requirements

The following table shows the tested and recommended configurations for reverse image search system.


| Component     | Recommended Configuration                                                    |
| -------- | ------------------------------------------------------------ |
| CPU      | Intel(R) Core(TM) i7-7700K CPU @ 4.20GHz                     |
| Memory   | 32GB                                                         |
| OS       | Ubuntu 18.04                                                 |
| Software | Milvus 0.10.0<br />pic_search_webclient  0.2.0<br />pic_search_webserver 0.10.0 |

### Source of data

This demo uses PASCAL VOC image set, which contains 17125 images that can be divided into 20 categories: human, animals (birds, cats, cows, dogs, horses, sheep, and more), means of transport (planes, bikes,boats, buses, cars, motorcycles, trains, and more), furniture and household appliances (bottles, chairs, tables, potted plants, sofas, TVs, and more)

Dataset size: ~ 2 GB.

Url for download: https://pan.baidu.com/s/1MjACqsGiLo3oiTMcxodtdA extraction code: v78m

> Note: You can also use other images for testing. This system supports images in formats of .jpg and .png.

### How to deploy the system

### GPU method

#### 1. Run Milvus docker

This demo uses Milvus 0.10.0. Refer to the [Install Milvus](https://milvus.io/cn/docs/v0.10.0/cpu_milvus_docker.md) for how to run Milvus docker.

#### 2. Run pic_search_webserver docker

```bash
$ docker run -d --name zilliz_search_images_demo \
-v ${IMAGE_PATH1}:/tmp/pic1 \
-v ${IMAGE_PATH2}:/tmp/pic2 \
-p 35000:5000 \
-e "DATA_PATH=/tmp/images-data" \
-e "MILVUS_HOST=${MILVUS_IP}" \
milvusbootcamp/pic-search-webserver:0.10.0
```

In the previous command line, `IMAGE_PATH1` and `IMAGE_PATH2` specify the paths where images are located. These locations are mapped to the docker container. After deployment, you can use `/tmp/pic1` and `/tmp/pic2` to load images. `MILVUS_HOST` specifies the IP address of the Milvus Docker host. Do not use loopback address "127.0.0.1". You do not have to modify other parts of the command line.

#### 3. Run pic-search-webclient docker

```bash
$ docker run --name zilliz_search_images_demo_web -d --rm -p 8001:80 \
-e API_URL=http://${WEBSERVER_IP}:35000 \
milvusbootcamp/pic-search-webclient:0.2.0
```

In the previous command line, WEBSERVER_IP specifies the server IP address that runs pic-search-webserver docker.

### How to perform reverse image search

After deployment, enter ` ${WEBCLIENT_IP}:8001` in the browser to open the interface for reverse image search. WEBCLIENT_IP specifies the server IP address that runs pic-search-webclient docker.

<img src="pic/web4.png" width = "650" height = "500" alt="arch" align=center />

Enter the path of an image folder in the pic_search_webserver docker container, such as /tmp/pic1. Click Load to load the pictures. The following screenshot shows the loading process:

<img src="pic/web2.png" width = "650" height = "500" alt="arch" align=center />

> Note: After clicking the Load button, it will take 1 to 2 seconds for the system to response. Please do not make multiple clicks.

It may take several minutes to load the images. The following screenshot shows the interface when image loading is successful.

<img src="pic/web3.png" width = "650" height = "500" alt="arch" align=center />

Select the image you want to search for.

<img src="pic/web5.png" width = "650" height = "500" alt="arch" align=center />

The system is tested and proved to be able to complete reverse image search within 1 second using the recommended configuration. To load images in other directories of the pic_search_webserver docker, specify the path in the textbox.

