# RaSa 2026

[Original repository](https://github.com/Flame-Chasers/RaSa)

## Repository structure

* `dataset/`: Store the 3 dataset.
* `pretrain/`: Store the pretrain ALBEF checkpoint.
* `source/`: The original RaSa code repository.

## How to run

1. Create **conda** environment:

    ```bash
    conda create -n rasa python=3.8
    conda activate rasa
    conda install pytorch==1.9.1 torchvision==0.10.1 transformers==4.8.1 timm==0.4.9 ruamel_yaml gdown -c pytorch -c conda-forge
    ```

2. Download **RSTPReid** [dataset](https://github.com/NjtechCVLab/RSTPReid-Dataset?tab=readme-ov-file#dataset-access):

    ```bash
    mkdir dataset
    cd dataset
    mkdir RSTPReid
    cd RSTPReid
    gdown 1HTeDZUVrZr6nL56ZlkYBNqjSWh3IGV2X
    unzip RSTPReid.zip
    rm RSTPReid.zip
    cd ../..
    ```

3. Split dataset:

    ```bash
    cd source
    python data_process.py --dataset_name "RSTPReid" --dataset_root_dir ../dataset/RSTPReid
    cd ..
    ```

4. Download pretrain ALBEF checkpoint:

    ```bash
    mkdir pretrain
    cd pretrain
    wget https://storage.googleapis.com/sfr-pcl-data-research/ALBEF/ALBEF.pth
    cd ..
    ```

5. Training (1 GPU):

    ```bash
    cd source
    python Retrieval.py \
    --config configs/PS_rstp_reid.yaml \
    --output_dir output/rstp-reid/train \
    --checkpoint ../pretrain/ALBEF.pth \
    --eval_mAP
    ```

    Or 4 GPU like the original repository:

    ```bash
    cd source
    python -m torch.distributed.run --nproc_per_node=4 --rdzv_endpoint=127.0.0.1:29501 \
    Retrieval.py \
    --config configs/PS_rstp_reid.yaml \
    --output_dir output/rstp-reid/train \
    --checkpoint ../pretrain/ALBEF.pth \
    --eval_mAP
    ```