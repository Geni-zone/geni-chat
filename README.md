# AcademiaGPT

# Install/Environment Setup
First clone the repository into wherever you want this to run:

```shell
git clone https://github.com/Dundric/AcademiaGPT.git
```

Second Create a new Conda Environment to run all the packages in:
[click here to learn out how to do this if you dont know](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html#starting-conda)

Next in order to set up the environment, navigate to the AcademiaGPT folder and install all requirements:

```shell
pip install -r requirements.txt
```

Next create a folder named 'models' in the Academia GPT folder

Download the default model (LLAMA2-7B-GGML 4 bit quantized):
[download here](https://huggingface.co/TheBloke/Llama-2-7B-GGML/blob/main/llama-2-7b.ggmlv3.q4_K_S.bin)

Then drag that model into the models folder

That should be all the basic setup you need.

(If you want a different LLAMA CPP SUPPORTING model just drag a different one into the model folder and change the models/llama-2-7b-chat.ggmlv3.q5_K_S.bin in the constants.py file to the path of the model you want).

## Test dataset
This repo uses Academic Nuclear Documents as an example.

## Instructions for ingesting your own dataset

When you run the app

The current default file types are .txt, .pdf, .csv, and .xlsx, if you want to use any other file type, you will need to convert it to one of the default file types.

It will create an index containing the local vectorstore. Will take time, depending on the size of your documents.
You can ingest as many documents as you want, and all will be accumulated in the local embeddings database. 
If you want to start from an empty database, delete the `index`.

Note: When you run this for the first time, it will download take time as it has to download the embedding model. In the subseqeunt runs, no data will leave your local enviroment and can be run without internet connection.



## Ask questions to your documents, locally!
In order to ask a question, run a command like:

```shell
chainlit run chainlitrun.py -w
```

And wait for the webapp to load

# How does it work?
Selecting the right local models and the power of `LangChain` you can run the entire pipeline locally, without any data leaving your environment, and with reasonable performance.

- `ingest.py` uses `LangChain` tools to parse the document and create embeddings locally using `InstructorEmbeddings`. Intructor Embeddings allows for topic specific embeddings so once you put a topic in it then stores the result in a local vector database using `Chroma` vector store. 
- `chainlitrun.py` uses the web app development library Chainlit and a local LLM (LLama2-7B in this case) to understand questions and create answers. The context for the answers is extracted from the local vector store using a similarity search to locate the right piece of context from the docs. 
- You can replace this local LLM with any other LLM that supports Llama cpp. Be sure to follow instrucitons in the beginning.
# System Requirements

## Python Version
To use this software, you must have Python 3.10 or later installed. Earlier versions of Python will not compile.

## C++ Compiler
If you encounter an error while building a wheel during the `pip install` process, you may need to install a C++ compiler on your computer.

### For Windows 10/11
To install a C++ compiler on Windows 10/11, follow these steps:

1. Install Visual Studio 2022.
2. Make sure the following components are selected:
   * Universal Windows Platform development
   * C++ CMake tools for Windows
3. Download the MinGW installer from the [MinGW website](https://sourceforge.net/projects/mingw/).
4. Run the installer and select the "gcc" component.

### NVIDIA Driver's Issues:
Follow this [page](https://linuxconfig.org/how-to-install-the-nvidia-drivers-on-ubuntu-22-04) to install NVIDIA Drivers. 


### M1/M2 Macbook users:

1- Follow this [page](https://developer.apple.com/metal/pytorch/) to build up PyTorch with Metal Performance Shaders (MPS) support. PyTorch uses the new MPS backend for GPU training acceleration. It is good practice to verify mps support using a simple Python script as mentioned in the provided link.

2- By following the page, here is an example of you may initiate in your terminal

```shell
xcode-select --install
conda install pytorch torchvision torchaudio -c pytorch-nightly
pip install chardet
pip install cchardet
pip uninstall charset_normalizer
pip install charset_normalizer
pip install pdfminer.six
pip install xformers
```


3- Create a new `verifymps.py` in the same directory (localGPT) where you have all files and environment.

	import torch
	if torch.backends.mps.is_available():
	    mps_device = torch.device("mps")
	    x = torch.ones(1, device=mps_device)
	    print (x)
	else:
	    print ("MPS device not found.")
    
 4- Find `instructor.py` and open it in VS Code to edit.
 
 The `instructor.py` is probably embeded similar to this: 
 	
	file_path = "/System/Volumes/Data/Users/USERNAME/anaconda3/envs/LocalGPT/lib/python3.10/site-packages/InstructorEmbedding/instructor.py"
 
 You can open the `instructor.py` and then edit it using this code:
 #### Open the file in VSCode
	subprocess.run(["open", "-a", "Visual Studio Code", file_path])
 
 Once you open `instructor.py` with VS Code, replace the code snippet that has `device_type` with the following codes:
 
         if device is None:
            device = self._target_device

        # Replace the line: self.to(device)
	
        if device in ['cpu', 'CPU']:
            device = torch.device('cpu')

        elif device in ['mps', 'MPS']:
            device = torch.device('mps')
        
        else:
            device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')

        self.to(device)
        

# Disclaimer
This is a test project to validate the feasibility of a fully local solution for question answering using LLMs and Vector embeddings. It is not production ready, and it is not meant to be used in production. Wizard-Vicuna-7B is based on the Llama model so that has the original Llama license. 
# AcademiaGPT
