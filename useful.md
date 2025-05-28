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