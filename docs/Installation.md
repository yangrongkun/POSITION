## Conda Environment
```
conda create -n position python=3.10 
conda activate position
conda install pytorch==2.5.1 torchvision==0.20.1 torchaudio==2.5.1 pytorch-cuda=12.4 -c pytorch -c nvidia
git clone https://github.com/IDEA-Research/Grounded-Segment-Anything.git
cd Grounded-Segment-Anything; python -m pip install -e segment_anything
pip install --no-build-isolation -e GroundingDINO
cd ../
pip install tabulate python-fcl lightning tyro open_clip_torch wandb h5py openai hydra-core distinctipy trimesh matplotlib rich opencv-python objaverse supervision==0.21.0
conda install -c pytorch faiss-gpu=1.8.0
pip install open3d pandas transformers shapely
pip install 'scipy<1.13.0'
pip install "git+https://github.com/facebookresearch/pytorch3d.git@stable"
pip install git+https://github.com/xinyu1205/recognize-anything.git
pip install fairscale
git clone https://github.com/krrish94/chamferdist.git
cd chamferdist
pip install .
cd ..
cd open_clip_mod
pip install .
cd ..
git clone https://github.com/gradslam/gradslam.git
cd gradslam
git checkout conceptfusion
pip install .
cd ..
git clone https://github.com/haotian-liu/LLaVA.git
cd LLaVA
# remove the "torch==2.1.2", "torchvision==0.16.2" in pyproject.toml in LLaVA
pip install -e .
cd ../..

# C++ Library
conda install -c conda-forge pybind11
cd ./ops
git clone https://github.com/gabyx/ApproxMVBB.git ApproxMVBB
mkdir Build; cd Build; cmake -DCMAKE_INSTALL_PREFIX="/usr/local/" ..;
make all; make install;
cd ../../../ops/ground_obb 
mkdir build; cd build; cmake ..; make;
```

Follow DuoduoCLIP to downloading the CAD embeddings: https://github.com/3dlg-hcvc/DuoduoCLIP.git
