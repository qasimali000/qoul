# qoul

Qoul is an experimental Automatic Speech Recognition system built using Kaldi. While it is understood that end-to-end ASRs give the best bang for the buck, an HMM-GMM based approach is actually quite auspicious for low-resource languages. There have been reports of people achieving appreciable WER (word error rates) by just using ~20-50 hours of speech data.

## Learn More

To grok the underlying working of an HMM-GMM based ASR, the following three articles are recommended.

* https://medium.com/@jonathan_hui/speech-recognition-phonetics-d761ea1710c0
* https://medium.com/@jonathan_hui/speech-recognition-feature-extraction-mfcc-plp-5455f5a69dd9
* https://medium.com/@jonathan_hui/speech-recognition-gmm-hmm-8bb5eff8b196

## Kaldi: A Frustration

As much as Kaldi is hailed as an outstanding ASR toolkit, it has one major problem: the documentation is indecipherable and perplexing for beginners. It takes hours upon hours of reading only to realize you're getting nowhere. Yes, there are some good tutorials but they are devoid of any explanation and only list out a series of steps to follow. This repository is an effort in that vein and I hope it'll save quite a few headaches for some.

## Speech Data

We used 3 different speech corpora. One was developed by us at FAST-NUCES while the other two were publicly available. Namely: [CSALT ITU Corpus](http://www.cle.org.pk/software/ling_resources/phoneticallyrichurduspeechcorpus.htm) & [RUMI Corpus](https://drive.google.com/drive/folders/1leTL6ueZGNe3aZdQAqjvOS5TJ0aDhyTC). We do not own the FAST-NUCES corpus and therefore have removed it before publishing this repository. You can still make use of this repository with the public corpora.

## Explanation

Kaldi works best with a specific directory structure with specific files at specific places. Here's the structure that we're using:

```
.
├── audio
│   ├── csalt
│   ├── csalt_transcription.txt
│   ├── rumi
│   └── rumi_transcription.txt
├── conf
│   └── mfcc.conf
│   ├── decode.config
├── data
│   ├── local
│   │   ├── corpus.txt
│   │   └── lang
│   │       └── lexicon.txt
│   ├── test
│   └── train
├── local
│   └── score.sh
├── cmd.sh
├── path.sh
├── prep.sh
├── mfcc_cmvn.sh
├── mono.sh
├── tri1.sh
├── tri2.sh
└── tri3.sh
├── sgmm2.sh
├── mmi_sgmm2.sh
├── qoul.ipynb
```

`audio` - Contains our speech data in form of wav files sampled at 16000Hz. Also contains the transcription files which map each wav file to their corresponding text counterpart. `audio/csalt` & `audio/rumi` are placeholder directories in which you should place the wav files from the public download links in the above section. Refer below for further instructions.

`conf` - Contains configuration files. By default it contains `mfcc.conf` & `decode.config` which contain config parameters for MFCC feature extraction and decoding processes. 

`data/local` - Contains `corpus.txt`, a giant corpus of Urdu sentences used to build our language model (using SRILM). `data/local/lang/lexicon.txt` is a file that maps each and every word in our speech data to a phonetic transcription. 

`local` - Contains `score.sh`, a script used in decoding. It is copied from the `wsj` example provided by Kaldi.

`cmd.sh` & `path.sh` - Contains some tunable parameters and paths for the following scripts.

`prep.sh` - Validates the `data` directory and creates a language model.

`mono.sh`, `tri1.sh`, `tri2.sh`, `tri3.sh`, `sgmm2.sh`, `mmi_sgmm2.sh` - Scripts to train different ASR models.

`qoul.ipynb` - A notebook that you can run to train a Kaldi model. Refer below for further instructions.

NOTE: A plethora of directories and files will be created when training Kaldi. The above structure is just a preset required by Kaldi.

## Instructions (Ubuntu 16.04+)

#### Installing Dependencies

```
sudo apt install automake autoconf sox libtool subversion gawk
```

#### Installing Kaldi

You need to download and compile/install kaldi. (Psst, it takes awfully long). Follow [this link](http://jrmeyer.github.io/asr/2016/01/26/Installing-Kaldi.html) or just execute the commands below.

```
git clone https://github.com/kaldi-asr/kaldi.git
cd kaldi/tools
extras/install_mkl.sh
extras/check_dependencies.sh
make -j 4
```
If `extras/check_dependencies.sh` complains about any missing dependencies, fix them first. Next use `nproc` to find out the number of processors you have and supply a reasonable amount to `make` as the `-j` flag just like above.

```
cd ../src/
./configure
make depend -j 4
make -j 4
```

As mentioned above, supply a reasonable amount of processors according to your hardware to `make` commands.

#### Installing SRILM

We're using SRILM to build our language model. By default, we build a 3-gram model. You may tune it by the `lm_order` variable in `cmd.sh`.

Download `srilm.tgz` available in this repository and copy it to `kaldi/tools`. Then `cd` into `kaldi/tools` and run the following script.

```
./install_srilm.sh
```

#### Cloning Qoul

Make sure to `cd` into `kaldi/egs` and run the following:

```
git clone git@github.com:parkerqueen/qoul.git
cd qoul
```

#### Downloading Speech Data

The transcription files for both the corpora are already placed in the repository. You just need to place the wav files as:

* CSALT:
  * Visit the [download link](http://www.cle.org.pk/software/ling_resources/phoneticallyrichurduspeechcorpus.htm) to download the corpus. After extraction, you shall find the wav files at `Recordings-Continuous/wav`. Copy all of them into `audio/csalt`.
* RUMI:
  * Download this [google drive folder](https://drive.google.com/drive/folders/1leTL6ueZGNe3aZdQAqjvOS5TJ0aDhyTC) and copy all the directories inside `Corpus/Recordings` into `audio/rumi`.

#### Running Notebook

Launch the notebook `qoul.ipynb` using Jupyter and you can run the cells in given order to train an ASR for Urdu.

