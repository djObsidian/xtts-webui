# XTTS-WebUI

## Nvidia RTX 50xx Blackwell patch

This fork is a quick fix that brings compatability with CUDA 12.8 and Nvidia Blackwell GPUs.

### Patch changelog:

1. Updated installation process and requirements.txt. All credits goes to [@quasiblob](https://github.com/quasiblob), see [his comment](https://github.com/daswer123/xtts-webui/issues/128#issuecomment-2888529586).
2. Fixed a crash in `create_multiple_reference` by importing `Path` from `pathlib` to handle file path operations properly.
3. tts_funcs.py: Added imports for `XttsConfig`, `XttsAudioConfig`, `BaseDatasetConfig`, and `XttsArgs` and included them in `add_safe_globals` to resolve `_pickle.UnpicklingError` during model loading with `weights_only=True`.



## About the Project
XTTS-Webui is a web interface that allows you to make the most of XTTS. There are other neural networks around this interface that will improve your results. You can also fine tune the model and get a high quality voice model.

![image](https://github.com/daswer123/xtts-webui/assets/22278673/89eb50c5-9a1e-41cf-8ae9-b734761716a6)

## Key Features
- Easy working with XTTSv2
- Batch processing for dubbing a large number of files
- Ability to translate any audio with voice saving
- Ability to improve results using neural networks and audio tools automatically
- Ability to fine tune the model and use it immediately
- Ability to use tools such as: **RVC**, **OpenVoice**, **Resemble Enhance**, both together and separately
- Ability to customize XTTS generation, all parameters, multiple speaking samples

## TODO
- [x] Add a status bar with progress and error information
- [x] Integrate training into the standard interface
- [ ] Add the ability to stream to check the result
- [ ] Add a new way to process text for voiceover
- [ ] Add the ability to customize speakers when batch processing
- [ ] Add API

## Installation

Tested with Windows 10, Python 3.12, CUDA 12.8, Visual Studio Build Tools 2022. 

**Please ensure you have Python >=3.10, CUDA 12.8 , Microsoft Builder Tools 2019 with c++ package, and ffmpeg installed**

Note: ffmpeg needs to be added in PATH

###  Windows manual install
Follow these steps for installation:
1. Ensure that `CUDA` is installed
2. Clone the repository: 
```
https://github.com/djObsidian/xtts-webui
```
3. Navigate into the directory: 
```
cd xtts-webui
````
4. Create a virtual environment:
```
python -m venv venv
```
5. Activate the virtual environment:
- On Windows use : 
```
venv\scripts\activate
```

6. Install torch and torchaudio:   
```
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu128
```

7. Install requirements:
```
pip install -r requirements.txt
```
8. Install deepspeed:
- For cmd users:
```
set DS_BUILD_AIO=0
set DS_BUILD_GDS=0
set DS_BUILD_OPS=0
set DS_BUILD_EVOFORMER_ATTN=0
set DS_BUILD_FP_QUANTIZER=0
```
- For PowerShell users:
```
$Env:DS_BUILD_OPS             = "0"
$Env:DS_BUILD_AIO             = "0"
$Env:DS_BUILD_GDS             = "0"
$Env:DS_BUILD_EVOFORMER_ATTN  = "0"
$Env:DS_BUILD_FP_QUANTIZER    = "0"
```
Then:
```
pip install git+https://github.com/microsoft/DeepSpeed.git
```




## Running The Application

To launch the interface please follow these steps:

#### Starting XTTS WebUI :
Activate your virtual environment:
```bash
venv/scripts/activate
```
or if you're on Linux,
```bash
source venv/bin/activate
```
Then start the webui for xtts by running this command:
```bash
python app.py
```

Here are some runtime arguments that can be used when starting the application:

| Argument | Default Value | Description |
| --- | --- | --- |
| -hs, --host | 127.0.0.1 | The host to bind to |
| -p, --port  | 8010       | The port number to listen on |
| -d, --device   | cuda    | Which device to use (cpu or cuda) |
| -sf,--speaker_folder  | speakers/   | Directory containing TTS samples|
|-o,--output  	|"output/"		|Output directory|
|-l,--language  	|"auto"		|Webui language, you can see the available translations in the i18n/locale folder.|
|-ms,--model-source  	|"local"			|Define the model source: 'api' for latest version from repository, api inference or 'local' for using local inference and model v2.0.2|
|-v,-version  			|"v2.0.2"				|You can specify which version of xtts to use. You can specify the name of the custom model for this purpose put the folder in models and specify the name of the folder in this flag|
|--lowvram   		||Enable low vram mode which switches the model to RAM when not actively processing|
|--deepspeed   		||Enable deepspeed acceleration. BROKEN DO NOT USE|
|--share   		 ||Allows sharing of interface outside local computer|
|--rvc     	 ||Enable RVC post-processing, all models should locate in rvc folder|

### TTS -> RVC

Module for RVC, you can enable the RVC module to postprocess the received audio for this you need to add the --rvc flag if you are running in the console or write it to the startup file

In order for the model to work in RVC settings you need to select a model that you must first upload to the voice2voice/rvc folder, the model and index file must be together, the index file is optional, each model must be in a separate folder.

## Differences between xtts-webui and the [official webui](https://github.com/coqui-ai/TTS/pull/3296)

### Data processing

1. Updated faster-whisper to 0.10.0 with the ability to select a larger-v3 model.
2. Changed output folder to output folder inside the main folder.
3. If there is already a dataset in the output folder and you want to add new data, you can do so by simply adding new audio, what was there will not be processed again and the new data will be automatically added
4. Turn on VAD filter
5. After the dataset is created, a file is created that specifies the language of the dataset. This file is read before training so that the language always matches. It is convenient when you restart the interface

### Fine-tuning XTTS Encoder

1. Added the ability to select the base model for XTTS, as well as when you re-training does not need to download the model again.
2. Added ability to select custom model as base model during training, which will allow finetune already finetune model.
3. Added possibility to get optimized version of the model for 1 click ( step 2.5, put optimized version in output folder).
4. You can choose whether to delete training folders after you have optimized the model
5. When you optimize the model, the example reference audio is moved to the output folder
6. Checking for correctness of the specified language and dataset language

### Inference

1. Added possibility to customize infer settings during model checking.

### Other

1. If you accidentally restart the interface during one of the steps, you can load data to additional buttons
2. Removed the display of logs as it was causing problems when restarted
3. The finished result is copied to the ready folder, these are fully finished files, you can move them anywhere and use them as a standard model
4. Added support for Japanese [here](https://github.com/daswer123/xtts-webui/issues/15#issuecomment-1869090189)

This project is tested with BrowserStack
