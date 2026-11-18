# CMAF: An Interpretable Cross-Modal Adversarial Fusion Method for Cancer Analysis
CMAF is an multi-modal fusion method utilized to fuse pathology data and multi-omic data

## Environment
- Python 3.10
- PyTorch 2.5.1 (CPU verified; CUDA builds also work)

```bash
pip install -r requirements.txt
```

## Data Preparing
WSI data and omic data can be downloaded from the [NIH Genomic Data Commons Data Portal](https://portal.gdc.cancer.gov/) and [UCSC Xena](https://xenabrowser.net/). We used the publicaly available [CLAM WSI-analysis toolbox](https://github.com/mahmoodlab/CLAM) to process WSI data (.svs format). 256x256 patches are first extracted from tissue regions of each WSI, and a pretrained res-Net50 is utilized to encode image patches into 1024-dim feature vector. The extracted features are saved as torch tensors of size Nx1024, where N is the number of patches for each WSI. These feature matrices are saved in .pt files and saved as following structure. <br />

```bash
DATA_DIRECTORY
    └──tcga_brca_20x_features/
          └──pt_files/
            └──slide1.pt
            └──slide2.pt
            └──.......
    └──tcga_gbmlgg_20x_features/
          └──pt_files/
            └──slide1.pt
            └──slide2.pt
            └──.......
    └──tcga_kirc_20x_features/
          └──pt_files/
            └──slide1.pt
            └──slide2.pt
            └──.......
```
The processed multi-omics data can be accessed in the ./datasets_csv_mutsig folder. 

## Project Structure
```
CMAF/
├── main.py                  # Training entry (survival prediction)
├── classification.py        # Classification entry
├── models/                  # CMAF / TransMIL / SNN models
├── datasets/                # Dataset loaders
├── utils/                   # Training / eval / loss utilities
├── datasets_csv_mutsig/     # Genomic CSV datasets (zip)
├── splits/5foldcv/          # 5-fold splits (brca / gbmlgg / kirc)
└── data/tcga_brca_20x_features/pt_files/   # 575 mock pathology features (.pt)
```

## Running
### Quick smoke test (Fold 0, 1 epoch, CPU verified)
```bash
python main.py --k_start 0 --k_end 1 --max_epochs 1 --gc 1 --overwrite
```
### Full training
Running the following command to train CMAF and perform survival predict
``` 
python main.py --split_dir tcga_CANCER_TYPE --fusion bilinear --data_root_dir PATH_TO_WSI --result_dir PATH_OF_RESULT --apply_mutsig --reg_type omic --lr 4e-4 --alignment --model_type cmaf
``` 
5-fold split files are prepared in ./splits/5foldcv <br />

## Simulated Data Notice
The 575 `.pt` files under `data/tcga_brca_20x_features/pt_files/` are randomly generated mock feature tensors (each `[N, 1024]`), NOT extracted from real pathology slides. They only serve to verify the training pipeline end-to-end. Training results on this data have **no clinical significance**.
