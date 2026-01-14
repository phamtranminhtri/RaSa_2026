# RaSa 2026

[Original repository](https://github.com/Flame-Chasers/RaSa)

## Repository structure

* `dataset/`: Store the 3 dataset.
* `pretrain/`: Store the pretrain ALBEF checkpoint.
* `source/`: The original RaSa code repository.

## How to run

> **Note**: If you want to run on Google Colab, first do these step at section [How to run in Google Colab](#how-to-run-in-google-colab)

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

5. Set the appropiate batch size configuration in `source/configs/PS_rstp_reid.yaml`. See section [Configure suitable batch size for your GPU](#configure-suitable-batch-size-for-your-gpu)

6. Training (1 GPU):

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

## Configure suitable batch size for your GPU

(*Recommended*) In `source/configs/PS_rstp_reid.yaml`, edit the parameter `batch_size_train`

* Original RaSa GPU: 4x **3090** = 96 GB VRAM

    ```yaml
    batch_size_train: 13
    ```

* Example laptop's GPU: **4060 Mobile**  = 8 GB VRAM

    ```yaml
    batch_size_train: 2
    ```

* Google Colab's free tier GPU: **T4** = 16 GB VRAM

    ```yaml
    batch_size_train: 4
    ```

## How to run in Google Colab

1. Open [Google Colab](https://colab.research.google.com/) and create a new notebook, then connect to **T4** GPU runtime.
2. Open the terminal and install [Miniconda](/docs/getting-started/miniconda/install#macos-linux-installation):

    ```bash
    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
    bash ./Miniconda3-latest-Linux-x86_64.sh -b
    source ~/miniconda3/bin/activate
    conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main
    conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/r
    ```

3. Clone the repository:

    ```bash
    git clone https://github.com/phamtranminhtri/RaSa_2026.git
    cd RaSa_2026
    ```

4. Follow the instruction from the begining in section [How to run](#how-to-run)