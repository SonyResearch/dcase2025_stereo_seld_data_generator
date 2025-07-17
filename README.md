# DCASE2025 Stereo SELD Data Generator
This data generator code aims to generate stereo sound event localization and detection (SELD) datasets from real or synthetic first-order Ambisonics (FOA) SELD recordings.
The code is used in the [DCASE2025 challenge](https://dcase.community/challenge2025/) to generate the [DCASE2025 Task3 Stereo SELD Dataset](https://zenodo.org/records/15087603) from the STARSS23 recordings.

The sampling and conversion procedures are followed.
First, the code randomly samples a 5-second clip from FOA audio / 360° video recordings and randomly selects a point-of-view.
According to the viewing angle, the code rotates the FOA audio and converts it to stereo audio, emulating a mid-side (M/S) stereo technique.
With the same viewing angle, the code converts the 360° video to a perspective video, in which the horizontal FOV is set to 100 degrees and the video resolution to 360x640 pixels.
The code also rotates the original direction-of-arrival (DOA) labels to new DOA labels centered at the fixed viewing angle.
The new DOA labels are compared with the FOV in the perspective video to get binary onscreen/offscreen event labels.

Please check the [task description paper](https://arxiv.org/abs/2507.12042) and [challenge webpage](https://dcase.community/challenge2025/) for details missing in this repository.

## Getting started
### Git clone
You can use git clone and move into the directory.
```bash
git clone https://github.com/SonyResearch/dcase2025_stereo_seld_data_generator.git
cd dcase2025_stereo_seld_data_generator
```

### Prepare python environment
The provided system has been tested on python 3.8.17.

You can prepare a python environment. E.g., if you use pyenv, you can run the following lines.
```bash
pyenv install 3.8.17
pyenv virtualenv 3.8.17 dcase2025_stereo_seld_data_generator
pyenv local dcase2025_stereo_seld_data_generator
```

You can continue to install it using the line below.
```bash
pip install -r requirements.txt
```

### Prepare source dataset
First, you prepare a source dataset, e.g., the STARSS23 dataset.
Then, you put the source data (`foa_dev`, `metadata_dev`, and `video_dev`) under `<source dir>`.
```
<source dir>            E.g., ../data_dcase2023_task3
├── foa_dev             Ambisonic format, 24kHz, four channels.
│   ├── dev-test-sony
│   ├── dev-test-tau
│   ├── dev-train-sony
│   └── dev-train-tau
├── metadata_dev        CSV format.
│   ├── dev-test-sony
│   ├── dev-test-tau
│   ├── dev-train-sony
│   └── dev-train-tau
└── video_dev           MP4 format. (It is only needed for audiovisual settings.)
    ├── dev-test-sony
    ├── dev-test-tau
    ├── dev-train-sony
    └── dev-train-tau
```

### Run data generator code
Before running `generate_stereo_seld_data.py`, please edit the `source_dir` and `target_dir` variables.
You can set `source_dir` as `<source dir>` in the above.
The code will make audio, video, and metadata files under `target_dir`/`tag_dataset`.
```
<target dir>/<tag dataset>  E.g., ../dataset/DCASE2025_Task3_Stereo_SELD_Dataset_Repro
├── stereo_dev
├── metadata_dev
└── video_dev
```

Then, you can run the code to make stereo audio, perspective video, and metadata files from the source FOA audio, 360° video, and metadata files.
```bash
python generate_stereo_seld_data.py
```

You can get the results below in your terminal and get data under the target directory.
```
100%|| 10/10 [00:36<00:00,  3.65s/it]
```

If you need more data, please edit `total_stereo_files` variable.
If you don't need video data, please switch `bin_make_video` to False.

### Run for synthetic source dataset
Below is an example of running the code for your own synthetic dataset, e.g., using SpatialScaper and/or SELDVisualSynth.

You put the new source data (`foa_dev`, `metadata_dev`, and `video_dev`) under `<source dir>`.
```
<source dir>            E.g., ../data_dcase2023_task3
├── foa_dev             Ambisonic format, 24kHz, four channels.
│   └── dev-train-synth An example of synth dir name.
├── metadata_dev        CSV format.
│   └── dev-train-synth
└── video_dev           MP4 format. (It is only needed for audiovisual settings.)
    └── dev-train-synth
```

Then, you can edit the `metadata_paths` variable in `generate_stereo_seld_data.py`.
```python
metadata_paths = sorted(glob.glob("{}/metadata_dev/dev-train-synth/*.csv".format(source_dir)))
```

Finally, you can run the code to make stereo audio, perspective video, and metadata files from your own synthetic FOA audio / 360° video recordings.
```bash
python generate_stereo_seld_data.py
```

## Repository structure
* `generate_stereo_seld_data.py` script serves as the main code to generate stereo audio, perspective video, and metadata files.
* `utils.py` script includes various utility functions.
* `aziele2valuexy_0deg_ufov100w640h360.npy` binary file is used to speed up checking if an event is onscreen or not.
* `make_aziele2valuexy_0deg.py` script is a reference for making the binary file.

## Citation
If you found this tool useful, please consider citing
```bibtex
@article{shimada2025stereo,
  title={Stereo Sound Event Localization and Detection with Onscreen/offscreen Classification},
  author={Shimada, Kazuki and Politis, Archontis and Roman, Iran R and Sudarsanam, Parthasaarathy and Diaz-Guerra, David and Pandey, Ruchi and Uchida, Kengo and Koyama, Yuichiro and Takahashi, Naoya and Shibuya, Takashi and Takahashi, Shusuke and Virtanen, Tuomas and Mitsufuji, Yuki},
  journal={arXiv preprint arXiv:2507.12042},
  year={2025}
}
```

## Reference
This code is built on the following papers and the open source repositories.

1. FOA audio conversion using a M/S stereo technique for SELD: ["Two vs. Four-Channel Sound Event Localization and Detection"](https://arxiv.org/abs/2309.13343)

2. 360° video conversion functions: https://github.com/sunset1995/py360convert

3. The STARSS23 dataset: ["STARSS23: An Audio-Visual Dataset of Spatial Recordings of Real Scenes with Spatiotemporal Annotations of Sound Events"](https://arxiv.org/abs/2306.09126)

4. Synthetic FOA audio recordings: ["Spatial Scaper: A Library to Simulate and Augment Soundscapes for Sound Event Localization and Detection in Realistic Rooms"](https://arxiv.org/abs/2401.12238)

5. Synthetic 360° video recordings: https://github.com/adrianSRoman/SELDVisualSynth

## License
This repository is under the MIT License.
