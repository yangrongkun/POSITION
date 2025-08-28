# Detail Parameter Explain for Each Step

## 1. Grounded-SAM segment image sequence
**Running Scripts:**
```bash
python src/segmentation/generate_gsa_results.py \
    --dataset_root ${DATA_ROOT} \
    --dataset_config /home/yrk/Desktop/program/POSITION/src/dataset/dataconfigs/replica/replica.yaml \
    --scene_id ${SCENE_NAME}  \
    --class_set ram \
    --stride 5 \
    --box_threshold 0.3 \
    --text_threshold 0.3 \
    --add_bg_classes \
    --exp_suffix withbg_allclasses
```

**Parameter_list:**

```bash
--dataset_root := "The root path to save corresponding specific dataset, e.g.(${PROGRAM_ROOT}/dataset/Replica or ScanNet)"
--dataset_config := "The config file for Grounded-SAM, stores in ${PROGRAM_ROOT}/src/dataset/dataconfigs/replica/replica.yaml"
--scene_id := "The scene name of specific scenes, e.g. room0 in replica, or corresponding scene name of custom data"
--class_set := "The method to obtain semantic category name for each images, ram represent utilize the RAM model to recognize category name automatically"
--stride := "For the whole image sequence, skip the stride steps to select one image for segmentation"
--box_threshold := "The box threshold for grounded-sam, default is 0.3"
--text_threshold := "The text threshold for grounded-sam, default is 0.3"
--exp_suffix := withbg_all_classes "Take the recognized background object such as wall or floor, ceiling into consideration"
```

## 2. Merge Image Sequence Segmentation into point cloud
**Running Scripts:**

```bash
python src/slam/cfslam_pipeline_batch.py   \
    dataset_root=${DATA_ROOT}  \
    dataset_config=/home/yrk/Desktop/program/POSITION/src/configs/dataconfigs/replica/replica.yaml   stride=5 \
    scene_id=${SCENE_NAME}     spatial_sim_type=overlap     mask_conf_threshold=0.25 \
    match_method=sim_sum     sim_threshold=1.2 \
    dbscan_eps=0.1 \
    gsa_variant=ram_withbg_allclasses \
    skip_bg=False \
    max_bbox_area_ratio=0.5 \
    save_suffix=overlap_maskconf0.25_simsum1.2_dbscan.1 \
    save_objects_all_frames=False \
#    denoise_interval=10 \
#    dbscan_min_points=30
```

**Parameter_list:**

```bash
dataset_root := "The root path to save corresponding specific dataset, e.g.(${PROGRAM_ROOT}/dataset/Replica or ScanNet)"
dataset_config := "The config file for Grounded-SAM, stores in ${PROGRAM_ROOT}/src/dataset/dataconfigs/replica/replica.yaml"
scene_id := "The scene name of specific scenes, e.g. room0 in replica, or corresponding scene name of custom data"
spatial_sim_type := overlap, "represent the multi-view point cloud fusion need to satisfy the overlap condition"
mask_conf_threshold := 0.25, "filter the masks with confidence lower than 0.25 in the first step."
match_method := sim_sum, "represent the multi-view fusion similarity threshold should be the sum of text and visual feature similarity"
sim_threshold := 1.2 , "the feature similarity threshold for multi-view"
dbscan_eps := "when merge multiview point cloud, utilize the DBSCAN algorithm to remove noise point cloud, dbscan_eps is the eps parameter in DBSCAN algorithm"
gsa_variant := ram_withbg_allclasses,  "the same as the parameter {gsa_variant} in the first step"
skip_bg := False ,  "Keep the background object information"
max_bbox_area_ratio := 0.5, "default to 0.5 for mask and bbox filtering"
save_suffix := "The suffix name for the multi-view association file saved"
save_objects_all_frames := "Save each frames segmentation result for visualization"
denoise_interval := 10,  "default is None, set this parameter to enable DBSCAN denoise point cloud every 10 frames of images"
dbscan_min_points := 30, "default is 10, the minimal point for DBSCAN cluster to remove noise"
```


## 3. Extract per object caption
**Running Scripts:**
```bash
python src/scene_graph/build_scenegraph_cfslam_v16.py \
    --mode extract-node-captions \
    --cachedir ${DATA_ROOT}/${SCENE_NAME}/sg_cache \
    --mapfile ${DATA_ROOT}/${SCENE_NAME}/pcd_saves/${MAPFILE} \
    --max_detections_score_per_object 10 \
    --max_detections_area_per_object 10
```

**Parameter_list:**

```bash
--mode := extract-node-captions , "Utilize the LLaVA model to extract captions for each object-centric cropped images obtained in Step 2"
--cache_dir := ${DATA_ROOT}/${SCENE_NAME}/sg_cache, "The directory to save captions file for each object"
--mapfile := ${DATA_ROOT}/${SCENE_NAME}/pcd_saves/${MAPFILE}, "The path of point cloud mapfile obtained in the step 2"
--max_detections_score_per_object := 10 , "Select the object-centric images with top 10 highest score to generate captions"
--max_detections_area_per_object := 10 , "Select the object-centric images with top 10 largest mask area to generate captions"
```


## 4. Crop Object Images
**Running Scripts:**
```bash
python src/scene_graph/object_image_crop.py \
    --scene_id ${SCENE_NAME} \
    --mapfile ${DATA_ROOT}/${SCENE_NAME}/pcd_saves/${MAPFILE} \
    --data_root ${DATA_ROOT} \
    --max_detections_score_per_object 10 \
    --max_detections_area_per_object 10 \
    --masking_option none \
```

**Parameter_list:**
```bash
--masking_option := (none or whiteout) , store the image by whether remove the background, none means keep background, whiteout means make the background white
--custom_data := True, whether utilized in custom_data scenes, if not remove this parameter

All other parameters in Step 4 share the same meaning as Step 3
```

## 5. DuoduoCLIP retrieval
**Running Scripts:**
```bash
python src/cad_recomposition/duoduoclip_text_image_retrieval.py \
    scene_id=${SCENE_NAME} \
    data_root=${DATA_ROOT} \
    mapfile=${DATA_ROOT}/${SCENE_NAME}/pcd_saves/${MAPFILE}
```

**Parameter_list:**
```bash
The config parameters of DuoduoCLIP retrieval is kept in ${PROGRAM_ROOT}/src/configs/duoduoclip/*.yaml

Please modify the CAD extracted duoduoclip embedding path in the main() function of duoduoclip_text_image_retrieval.py

All other parameters in Step 5 share the same meaning as Step 3
```

# 6. DINOv2 retrieval
**Running Scripts:**
```bash
python src/cad_recomposition/dinov2_retrieval.py \
    --scene_id ${SCENE_NAME} \
    --data_root ${DATA_ROOT}
```

**Parameter_list:**
```bash
Please modify the CAD compressed .h5 format images path and model_id_path in the main() function of dinov2_retrieval.py

All the parameters in Step 6 share the same meaning as Step 3
```

## 7. CAD recomposition
**Running Scripts:**
```bash
python src/cad_recomposition/cad_recomposition_replica.py \
    --scene_id ${SCENE_NAME} \
    --data_root ${DATA_ROOT} \
    --similarity_threshold 1.8 \
    --mapfile ${DATA_ROOT}/${SCENE_NAME}/pcd_saves/${MAPFILE}
```
**Parameter_list:**
```bash
--similarity_threshold := 1.8, "The similarity threshold between different instance, the similarity between different instance is calculated by the of CLIP text and image feature cosine similarity, highest is 2.0"
Other parameters in Step 7 share the same meaning as Step 3
```

## 8. Physical pose optimization
**Running Scripts:**
```bash
python src/cad_recomposition/physical_pose_optimization.py \
    --scene_id ${SCENE_NAME} \
    --data_root ${DATA_ROOT} \
    --mapfile ${DATA_ROOT}/${SCENE_NAME}/${MAPFILE}
```

```bash
python src/cad_recomposition/collide_optimization.py \
    --scene_id ${SCENE_NAME} \
    --data_root ${DATA_ROOT} \
    --mapfile ${DATA_ROOT}/${SCENE_NAME}/${MAPFILE}
```

**Parameter_list:**
```bash
All the parameters in Step 6 share the same meaning as Step 3
```


