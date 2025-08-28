# CAD Database
### 1. Download the CAD dataset to your local directories
3D Future: https://tianchi.aliyun.com/specials/promotion/alibaba-3d-future

ABO: https://amazon-berkeley-objects.s3.amazonaws.com/index.html

ShapeNet: https://shapenet.org/

Objaverse-LVIS: https://objaverse.allenai.org/

### 2. Prepare the shape embeddings and multi-view rendered images for CAD assets
```
export PYTHONPATH="${PROGRAM_BASE_PATH}:${PYTHONPATH}"
# download the multi-view rendered images follow Duoduo-CLIP
python src/preprocess/download_shapenet_3dfuture_abo_mv_embeddings.py

# Extract shape embeddings from multi-view images
python src/preprocess/extract_image_embeddings.py

```

# Replica

### 1. RGB-D sequences
For the RGB-D images as input, POSITION follows the ConceptGraph to prepare the dataset using [Replica](https://github.com/facebookresearch/Replica-Dataset). Instead of the original Replica dataset, download the scanned RGB-D trajectories of the Replica dataset provided by [Nice-SLAM](https://github.com/cvg/nice-slam). It contains rendered trajectories using the mesh models provided by the original Replica datasets. 
Download the Replica RGB-D scan dataset using the downloading [script](https://github.com/cvg/nice-slam/blob/master/scripts/download_replica.sh) in [Nice-SLAM](https://github.com/cvg/nice-slam#replica-1) and set `$REPLICA_ROOT` to its saved path.

```bash
export REPLICA_ROOT=datasets/replica_online
```

POSITION can also be easily run on other dataset. See `dataset/datasets_common.py` for how to write your own dataloader. 


### 2. 3D Point cloud with multi-view images
For the 3D point cloud as input, POSITION follows the Open Yolo3D to process 3D scene data with images.
A replica folder with the following format will be generated.
```
./data
  └── replica
    └── ground_truth
            ├── office0.txt
            └── ...
    └── office0
            ├── poses                            <- folder with camera poses
            │      ├── 0.txt 
            │      ├── 1.txt 
            │      └── ...  
            ├── color                           <- folder with RGB images
            │      ├── 0.jpg (or .png/.jpeg)
            │      ├── 1.jpg (or .png/.jpeg)
            │      └── ...  
            ├── depth                           <- folder with depth images
            │      ├── 0.png (or .jpg/.jpeg)
            │      ├── 1.png (or .jpg/.jpeg)
            │      └── ...  
            ├── intrinsics.txt                 <- camera intrinsics
            │ 
            └── office0_mesh.ply      <- raw point cloud of the scene
    └── ... 
```


# ScanNet200
### 1. RGB-D sequences
Download the full scene dataset in ScanNetv2, including RGBD sequences, 3D scene mesh.

For the RGB-D sequences, the data folder should have the following structure
```
./data
  └── scannet200
    └── scene0011_00
            ├── pose                            <- folder with camera poses
            │      ├── 0.txt 
            │      ├── 1.txt 
            │      └── ...  
            ├── color                           <- folder with RGB images
            │      ├── 0.jpg (or .png/.jpeg)
            │      ├── 1.jpg (or .png/.jpeg)
            │      └── ...  
            ├── depth                           <- folder with depth images
            │      ├── 0.png (or .jpg/.jpeg)
            │      ├── 1.png (or .jpg/.jpeg)
            │      └── ...  
            ├── intrinsic                <- camera intrinsics
    └── ... 
```


For 3D Point cloud with multiview iamges, ScanNet200 data folder should have the following structure
```
./data
  └── scannet200
    └── ground_truth
            ├── scene0011_00.txt
            └── ...
    └── scene0011_00
            ├── poses                            <- folder with camera poses
            │      ├── 0.txt 
            │      ├── 1.txt 
            │      └── ...  
            ├── color                           <- folder with RGB images
            │      ├── 0.jpg (or .png/.jpeg)
            │      ├── 1.jpg (or .png/.jpeg)
            │      └── ...  
            ├── depth                           <- folder with depth images
            │      ├── 0.png (or .jpg/.jpeg)
            │      ├── 1.png (or .jpg/.jpeg)
            │      └── ...  
            ├── intrinsics.txt                 <- camera intrinsics
            │ 
            └── 0011_00.npy                <- preprocessed point cloud of the scene
            │ 
            └── scene0011_00_vh_clean_2.ply      <- raw point cloud of the scene
    └── ... 
```




