---
title:      "Starting with Kaldi"
date:       2020-08-14 10:00:00 +0700
categories: ASR Kaldi
---
## Some prefacing notes

___Even more unnotable note__: this is a really rough getting started guide to Kaldi that I also use to refresh myself on Kaldi things. I'll probably write another document later on going into more detail about the structures and whatnot of Kaldi._

The following downloading and compiling section works best on MacOS and Linux. On Windows, using WSL 2.0 may work (I have not personally tried it extensively) but line endings have to be fixed with a tool like `dos2unix`. Docker should also work on Windows machines.

## Downloading and compiling

Before starting, the system should have some C compiler installed (usually `gcc`) and some other required libraries. If any related errors occur, just install the required packages with the appropriate package manager, e.g. `apt` on Debian and Ubuntu, `pacman` for Arch  etc.

From the command line, inside your working repository, download the github repository with:

{% highlight console %}
foo@bar:~$ git clone https://github.com/kaldi-asr/kaldi.git
{% endhighlight %}

Enter the directory created (usually named `kaldi` or whatever name was entered when pulling from Github):

{% highlight console %}
foo@bar:~$ cd kaldi
foo@bar:~/kaldi$
{% endhighlight %}

Reading the `INSTALL` file gives the overall instructions:

{% highlight console %}
foo@bar:~/kaldi$ cat INSTALL
This is the official Kaldi INSTALL. Look also at INSTALL.md for the git mirror installation.
[Option 1 in the following does not apply to native Windows install, see windows/INSTALL or following Option 2]

Option 1 (bash + makefile):

  Steps:
    (1)
    go to tools/  and follow INSTALL instructions there.

    (2)
    go to src/ and follow INSTALL instructions there.

Option 2 (cmake):

    Go to cmake/ and follow INSTALL.md instructions there.
    Note, it may not be well tested and some features are missing currently.
{% endhighlight %}

With this, move into the `tools` directory and read the `INSTALL` file there:

{% highlight console %}
foo@bar:~/kaldi$ cd tools
foo@bar:~/kaldi/tools$ cat INSTALL
To check the prerequisites for Kaldi, first run

  extras/check_dependencies.sh

and see if there are any system-level installations you need to do. Check the
output carefully. There are some things that will make your life a lot easier
if you fix them at this stage. If your system default C++ compiler is not
supported, you can do the check with another compiler by setting the CXX
environment variable, e.g.

  CXX=g++-4.8 extras/check_dependencies.sh

Then run

  make

which by default will install ATLAS headers, OpenFst, SCTK and sph2pipe.
OpenFst requires a relatively recent C++ compiler with C++11 support, e.g.
g++ >= 4.7, Apple clang >= 5.0 or LLVM clang >= 3.3. If your system default
compiler does not have adequate support for C++11, you can specify a C++11
compliant compiler as a command argument, e.g.

  make CXX=g++-4.8

If you have multiple CPUs and want to speed things up, you can do a parallel
build by supplying the "-j" option to make, e.g. to use 4 CPUs

  make -j 4

In extras/, there are also various scripts to install extra bits and pieces that
are used by individual example scripts.  If an example script needs you to run
one of those scripts, it will tell you what to do.
{% endhighlight %}

First, run `check_dependencies.sh` and install any of the missing tools required (as mentioned above). Then, run `make` or if you want to compile using multiple threads `make -j <num-threads>`, where `<num-threads>` can be found using the `nproc` command:

{% highlight console %}
foo@bar:~/kaldi/tools$ extras/check_dependencies.sh
extras/check_dependencies.sh: all OK.
{% endhighlight %}

{% highlight console %}
foo@bar:~/kaldi/tools$ nproc
4
foo@bar:~/kaldi/tools$ make -j 4
            .
            .
            .
Warning: IRSTLM is not installed by default anymore. If you need IRSTLM
Warning: use the script extras/install_irstlm.sh
All done OK.
foo@bar:~/kaldi/tools$
{% endhighlight %}

Just in case, install IRSTLM as well for when needed to make own language models, but you can always come back to this later:

{% highlight console %}
foo@bar:~/kaldi/tools$ extras/install_irstlm.sh
{% endhighlight %}

The next part of the first `INSTALL` file is to enter the `src` directory and work from there:

{% highlight console %}
foo@bar:~/kaldi/tools$ cd ../src
foo@bar:~/kaldi/src$ cat INSTALL

These instructions are valid for UNIX-like systems (these steps have
been run on various Linux distributions; Darwin; Cygwin).  For native Windows
compilation, see ../windows/INSTALL.

You must first have completed the installation steps in ../tools/INSTALL
(compiling OpenFst; getting ATLAS and CLAPACK headers).

The installation instructions are

  ./configure --shared
  make depend -j 8
  make -j 8

Note that we added the "-j 8" to run in parallel because "make" takes a long
time.  8 jobs might be too many for a laptop or small desktop machine with not
many cores.

For more information, see documentation at http://kaldi-asr.org/doc/
and click on "The build process (how Kaldi is compiled)".
{% endhighlight %}

Pretty straightforward from here on by following the instructions here, if the tools compiled successfully. First, run the `./configure` script:

{% highlight console %}
foo@bar:~/kaldi/src$ ./configure --shared
Configuring...
            .
            .
            .
SUCCESS
{% endhighlight %}

Then, `make depend`, while also using the appropriate number of threads, as above:

{% highlight console %}
foo@bar:~/kaldi/src$ make depend -j 4
            .
            .
            .
{% endhighlight %}

Then, `make`:

{% highlight console %}
foo@bar:~/kaldi/src$ make -j 4
{% endhighlight %}

### Important note for MacOS:

You may run into a few errors concerning commandline tools and arguments with certain scripts in Kaldi, such as `awk`, `find` and `sed`. This is because MacOS uses the BSD versions of these tools, as opposed to the GNU versions on Linux.

You can check this with `man`, to see the manual of the tool required (e.g. `man awk`) and it may say BSD on the first few lines. 

Fix this by installing the GNU version of the tool in the appropriate package, either from Homebrew or alternative and change the path in your shell config script to use the new GNU version.  
An important one is `coreutils`, but a few others may be needed as well:

{% highlight console %}
foo@bar:~$ brew install coreutils findutils gnu-tar gnu-sed gawk gnutls gnu-indent gnu-getopt grep
{% endhighlight %}

These tools can be run with the g prefix in front of their names, such as `gawk` instead of `awk`.

Otherwise, to change their path, check their brew description

{% highlight console %}
foo@bar:~$ brew info coreutils
coreutils: stable 8.21
http://www.gnu.org/software/coreutils
Depends on: xz
/usr/local/Cellar/coreutils/8.20 (208 files, 9.4M)
/usr/local/Cellar/coreutils/8.21 (210 files, 9.6M) *
https://github.com/mxcl/homebrew/commits/master/Library/Formula/coreutils.rb
==> Caveats
All commands have been installed with the prefix 'g'.

If you really need to use these commands with their normal names, you
can add a "gnubin" directory to your PATH from your bashrc like:

    PATH="/usr/local/opt/coreutils/libexec/gnubin:$PATH"

Additionally, you can access their man pages with normal names if you add
the "gnuman" directory to your MANPATH from your bashrc as well:

    MANPATH="/usr/local/opt/coreutils/libexec/gnuman:$MANPATH"
{% endhighlight %}

## Testing out Kaldi

To see if Kaldi works, try out the `yesno` model, which classifies if the person if saying a "yes" or a "no".

First, enter the examples directory and into the `yesno` one:

{% highlight console %}
foo@bar:~/kaldi/src$ cd ../egs
foo@bar:~/kaldi/egs$ ls
ami                chime1                   fisher_english  librispeech  sprakbanken  tidigits      yesno
aspire             chime2                   fisher_swbd     lre          sre08        timit
aurora4            chime3                   gale_arabic     lre07        sre10        voxforge
babel              csj                      gale_mandarin   README.txt   swahili      vystadial_cz
bn_music_speech    farsdat                  gp              reverb       swbd         vystadial_en
callhome_egyptian  fisher_callhome_spanish  hkust           rm           tedlium      wsj
foo@bar:~/kaldi/egs$ cd yesno
foo@bar:~/kaldi/egs/yesno$ ls
README.txt  s5
foo@bar:~/kaldi/egs/yesno$ cat README.txt


The "yesno" corpus is a very small dataset of recordings of one individual
saying yes or no multiple times per recording, in Hebrew.  It is available from
http://www.openslr.org/1.
It is mainly included here as an easy way to test out the Kaldi scripts.

The test set is perfectly recognized at the monophone stage, so the dataset is
not exactly challenging.

The scripts are in s5/.

foo@bar:~/kaldi/egs/yesno$ cd s5
foo@bar:~/kaldi/egs/yesno/s5$
{% endhighlight %}

Run the `./run.sh` file and it should run without any errors:

{% highlight console %}
foo@bar:~/kaldi/egs/yesno/s5$ ./run.sh
            .
            .
            .
decode.sh: feature type is delta
%WER 0.00 [ 0 / 232, 0 ins, 0 del, 0 sub ] [PARTIAL] exp/mono0a/decode_test_yesno/wer_10
{% endhighlight %}

## Some important files and directories in Kaldi

The Kaldi directory has a few important sub-directories.  
The `tools` directory is where the important things Kaldi depends on can be installed, such as OpenFst.  
The `src` directory is where the various C and C++ source code is held. These are called from shell scripts in the recipes that come with Kaldi.  
The `egs` directory is where the example recipes are. These, like the `yesno` example, can be run (mostly) directly with minimal tweaking for out-of-the-box ASR systems.

Before scripts in `egs` can be directly run however, you have to prepare data to run the recipes on. These files have to be put into a `data` directory where the `run.sh` script and others can reference.

Looking at the `yesno` example, before running `./run.sh`:

{% highlight console %}
foo@bar:~/kaldi/egs/yesno$ tree
.
├── README.txt
└── s5
    ├── conf
    │   ├── mfcc.conf
    │   └── topo_orig.proto
    ├── input
    │   ├── lexicon_nosil.txt
    │   ├── lexicon.txt
    │   ├── phones.txt
    │   └── task.arpabo
    ├── local
    │   ├── create_yesno_txt.pl
    │   ├── create_yesno_waves_test_train.pl
    │   ├── create_yesno_wav_scp.pl
    │   ├── prepare_data.sh
    │   ├── prepare_dict.sh
    │   ├── prepare_lm.sh
    │   └── score.sh
    ├── path.sh
    ├── run.sh
    ├── steps -> ../../wsj/s5/steps
    └── utils -> ../../wsj/s5/utils

6 directories, 16 files
{% endhighlight %}

After `./run.sh`, an extra `data` directory is created

{% highlight console %}
foo@bar:~/kaldi/egs/yesno/s5$ tree data
./data/
├── lang
│   ├── L_disambig.fst
│   ├── L.fst
│   ├── oov.int
│   ├── oov.txt
│   ├── phones
│   │   ├── align_lexicon.int
│   │   ├── align_lexicon.txt
│   │   ├── context_indep.csl
│   │   ├── context_indep.int
│   │   ├── context_indep.txt
│   │   ├── disambig.csl
│   │   ├── disambig.int
│   │   ├── disambig.txt
│   │   ├── extra_questions.int
│   │   ├── extra_questions.txt
│   │   ├── nonsilence.csl
│   │   ├── nonsilence.int
│   │   ├── nonsilence.txt
│   │   ├── optional_silence.csl
│   │   ├── optional_silence.int
│   │   ├── optional_silence.txt
│   │   ├── roots.int
│   │   ├── roots.txt
│   │   ├── sets.int
│   │   ├── sets.txt
│   │   ├── silence.csl
│   │   ├── silence.int
│   │   ├── silence.txt
│   │   ├── wdisambig_phones.int
│   │   ├── wdisambig.txt
│   │   └── wdisambig_words.int
│   ├── phones.txt
│   ├── topo
│   └── words.txt
├── lang_test_tg
│   ├── G.fst
│   ├── L_disambig.fst
│   ├── L.fst
│   ├── oov.int
│   ├── oov.txt
│   ├── phones
│   │   ├── align_lexicon.int
│   │   ├── align_lexicon.txt
│   │   ├── context_indep.csl
│   │   ├── context_indep.int
│   │   ├── context_indep.txt
│   │   ├── disambig.csl
│   │   ├── disambig.int
│   │   ├── disambig.txt
│   │   ├── extra_questions.int
│   │   ├── extra_questions.txt
│   │   ├── nonsilence.csl
│   │   ├── nonsilence.int
│   │   ├── nonsilence.txt
│   │   ├── optional_silence.csl
│   │   ├── optional_silence.int
│   │   ├── optional_silence.txt
│   │   ├── roots.int
│   │   ├── roots.txt
│   │   ├── sets.int
│   │   ├── sets.txt
│   │   ├── silence.csl
│   │   ├── silence.int
│   │   ├── silence.txt
│   │   ├── wdisambig_phones.int
│   │   ├── wdisambig.txt
│   │   └── wdisambig_words.int
│   ├── phones.txt
│   ├── tmp
│   │   ├── CLG_1_0.fst
│   │   ├── disambig_ilabels_1_0.int
│   │   ├── ilabels_1_0
│   │   └── LG.fst
│   ├── topo
│   └── words.txt
├── local
│   ├── dict
│   │   ├── lexiconp.txt
│   │   ├── lexicon.txt
│   │   ├── lexicon_words.txt
│   │   ├── nonsilence_phones.txt
│   │   ├── optional_silence.txt
│   │   └── silence_phones.txt
│   ├── lang
│   │   ├── align_lexicon.txt
│   │   ├── lexiconp_disambig.txt
│   │   ├── lexiconp.txt
│   │   ├── lex_ndisambig
│   │   ├── phone_map.txt
│   │   └── phones
│   ├── lm_tg.arpa
│   ├── test_yesno.txt
│   ├── test_yesno_wav.scp
│   ├── train_yesno.txt
│   ├── train_yesno_wav.scp
│   ├── waves_all.list
│   ├── waves.test
│   └── waves.train
├── test_yesno
│   ├── cmvn.scp
│   ├── feats.scp
│   ├── spk2utt
│   ├── split1
│   │   └── 1
│   │       ├── cmvn.scp
│   │       ├── feats.scp
│   │       ├── spk2utt
│   │       ├── text
│   │       ├── utt2spk
│   │       └── wav.scp
│   ├── text
│   ├── utt2spk
│   └── wav.scp
└── train_yesno
    ├── cmvn.scp
    ├── feats.scp
    ├── spk2utt
    ├── split1
    │   └── 1
    │       ├── cmvn.scp
    │       ├── feats.scp
    │       ├── spk2utt
    │       ├── text
    │       ├── utt2spk
    │       └── wav.scp
    ├── text
    ├── utt2spk
    └── wav.scp

14 directories, 115 files
{% endhighlight %}

Some files of note are `wav.scp`, `segments` and `utt2spk`.

`wav.scp` links the actual audio file to a "recording ID", which can be referenced later on in other files. Its format is as:

{% highlight console %}
<recording-id> <audio-file-path>
e.g.
FILE01 /exports/set1/file01.wav
{% endhighlight %}

`segments` indicates the segments in the audio file where speech occurs. It links an "utterance ID" to the correct start and end time (in seconds) in the audio file:

{% highlight console %}
<utterance-id> <recording-id> <start-time> <end-time>
e.g.
FILE01_0002-0012 FILE01 0.02 0.12
FILE01_0031-0103 FILE01 0.31 1.03
{% endhighlight %}

`utt2spk` links the utterance to the speaker, which may also be the recording ID depending on the naming scheme:

{% highlight console %}
<utterance-id> <speaker-id>
e.g.
FILE01_0002-0012 FILE01
FILE01_0031-0103 FILE01
{% endhighlight %}

This means in some cases, such as in _diarization_, the `utt2spk` file can be created with the shell command:

{% highlight console %}
awk '{print $1, $2}' segments > utt2spk
{% endhighlight %}
