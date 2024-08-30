# General-Fusion-Neutron-Spectrometer

**Data Analysis with the PicoTDC**
This repository contains Python code that can be used to analyze the data taken using Midas.

Almost all the code files use the function midas.filereader, and in order to run the code, you will have to install the Midas module on your console, as well as the numpy and matplotlib libraries.
**Note that these instructions are for Windows OS

**Instructions for installing Midas and setting up a virtual environment**
Make sure that Python 3.12 is installed on your machine, otherwise you can manually download it from the app store. 
1) install the midas module
   open Windows Powershell Terminal and enter this command:
   git clone https://bitbucket.org/tmidas/midas.git
2) create a virtual environment by running the following commands 
   cd midas
   python3 -m venv midas_env
   .\midas_env\Scripts\activate
   pip install .
   pip install -e C:/Users/bilny/midas/python (replace with your path to midas, i.e pip install -e (Your/Path/To midas)/midas/python )
   pip install jupyter notebook
   pip install matplotlib
   pip install numpy
   pip install lz4
4) Now run jupyter notebook by using the following command
   Jupyter Notebook


