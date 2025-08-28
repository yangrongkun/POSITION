# Detail Parameter Explain for Custom Data

## 1. Processing Custom data

**Running Scripts:**
```bash
python src/preprocess/custom_data_preprocess.py \
    --scene_id ${SCENE_NAME} \
    --data_root ${DATA_ROOT}
```


**Parameter_list:**
```bash
--scene_id := shenzhen-G14F-metacam  # The scene name of custom data
--dataset_root := "The root path to save corresponding specific dataset, e.g.(${PROGRAM_ROOT}/dataset/custom_data)"
```

## 2~8 CAD Recomposition Steps
These Steps share the same parameter meaning as [Parameter explain guide](./Parameter_explain.md) 
Please Add the parameter "--custom_data" in Step 2 and Step 3