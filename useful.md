Merge vocal and inst (lower accompaniment a bit):
```
ffmpeg -i /root/music_wav/Love_story_vocal.wav_0key_kono_wav_sovdiff_crepe.wav -i /root/music/Love_story_inst.wav -filter_complex \
"[1:a]volume=0.7[a1];[0:a][a1]amix=inputs=2:duration=longest" \
-y love_story.wav
```

Base model:
```
https://huggingface.co/kingple/sovits4/tree/main
https://huggingface.co/datasets/ms903/Diff-SVC-refactor-pre-trained-model/resolve/main/fix_pitch_add_vctk_600k/model_0.pt
```

Full inference:
```
python inference_main.py -m /root/so-vits-svc/logs/44k/G_8800.pth -c configs/config.json -n Love_story_vocal.wav -t 0 -s "kono_wav" -shd -f0p crepe -dm /root/so-vits-svc/logs/44k/diffusion/model_24000.pt
```

Diffusion only:
```
python inference_main.py -m  /root/so-vits-svc/logs/44k/diffusion/model_24000.pt -c configs/config.json -n Love_story_vocal.wav -t 0 -s "kono_wav"
```

## 📥 Pre-trained Model Files

**1. Using contentvec as speech encoder(recommended)**

`vec768l12` and `vec256l9` require the encoder

- ContentVec: [checkpoint_best_legacy_500.pt](https://ibm.box.com/s/z1wgl1stco8ffooyatzdwsqn2psd9lrr)
  - Place it under the `pretrain` directory

Or download the following ContentVec, which is only 199MB in size but has the same effect:
- ContentVec: [hubert_base.pt](https://huggingface.co/lj1995/VoiceConversionWebUI/resolve/main/hubert_base.pt)
  - Change the file name to `checkpoint_best_legacy_500.pt` and place it in the `pretrain` directory

```shell
# contentvec
wget -P pretrain/ https://huggingface.co/lj1995/VoiceConversionWebUI/resolve/main/hubert_base.pt -O checkpoint_best_legacy_500.pt
# Alternatively, you can manually download and place it in the hubert directory
```

**2.**

- Pre-trained model files: `G_0.pth` `D_0.pth`
  - Place them under the `logs/44k` directory

- Diffusion model pretraining base model file: `model_0.pt`
  - Put it in the `logs/44k/diffusion` directory

**3. Diffusion Model**
If you are using the `NSF-HIFIGAN enhancer` or `shallow diffusion`, you will need to download the pre-trained NSF-HIFIGAN model.

- Pre-trained NSF-HIFIGAN Vocoder: [nsf_hifigan_20221211.zip](https://github.com/openvpi/vocoders/releases/download/nsf-hifigan-v1/nsf_hifigan_20221211.zip)
  - Unzip and place the four files under the `pretrain/nsf_hifigan` directory

```shell
# nsf_hifigan
wget -P pretrain/ https://github.com/openvpi/vocoders/releases/download/nsf-hifigan-v1/nsf_hifigan_20221211.zip
unzip -od pretrain/nsf_hifigan pretrain/nsf_hifigan_20221211.zip
# Alternatively, you can manually download and place it in the pretrain/nsf_hifigan directory
# URL: https://github.com/openvpi/vocoders/releases/tag/nsf-hifigan-v1
```

## 🛠️ Preprocessing

### 0. Slice audio

### 1. Resample to 44100Hz and mono

### 2. Automatically split the dataset into training and validation sets, and generate configuration files.

### 3. Generate hubert and f0


## 🏋️‍ Training

### Sovits Model

```shell
python train.py -c configs/config.json -m 44k
```

### Diffusion Model (optional)

If the shallow diffusion function is needed, the diffusion model needs to be trained. The diffusion model training method is as follows:

```shell
python train_diff.py -c configs/diffusion.yaml
```

During training, the model files will be saved to `logs/44k`, and the diffusion model will be saved to `logs/44k/diffusion`

## 🤖 Inference

```
python inference_main.py -m /root/so-vits-svc/logs/44k/G_8800.pth -c configs/config.json -n Love_story_vocal.wav -t 0 -s "kono_wav" -shd -f0p crepe -dm /root/so-vits-svc/logs/44k/diffusion/model_24000.pt
```
- use `-f0p crepe` mean pooling to deal with dumb cases, though out of tune sometime
- Shallow diffusion solves some electrical sound problems after use.