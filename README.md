# General-Fusion-Neutron-Spectrometer

**Data Analysis with the PicoTDC**

This repository contains Python code that can be used to analyze the TDC data taken using Midas.

Almost all the code files use the function midas.file_reader, and in order to run the code you will have to install the Midas module on your console, as well as the numpy and matplotlib libraries.

**Note that these instructions are for Windows OS**

**Instructions for installing Midas and setting up a virtual environment**

Make sure that Python 3.12 is installed on your machine, otherwise you can manually download it from the app store. 

1) install the midas module
   - open Windows Powershell Terminal and enter this command:
   - `git clone https://bitbucket.org/tmidas/midas.git`
2) create a virtual environment by running the following commands 
   - `cd midas`
   - `python3 -m venv midas_env`
   - `.\midas_env\Scripts\activate`
   - `pip install .`
   - `pip install -e C:/Users/bilny/midas/python` (replace with your path to midas, i.e pip install -e (Your/Path/To midas)/midas/python )
   - `pip install jupyter notebook`
   - `pip install matplotlib`
   - `pip install numpy`
   - `pip install lz4`
4) Now run jupyter notebook by using the following command
   - `Jupyter Notebook`

**Important information for using code scripts in the repository**

- When running the scripts, make sure that the channel you are reading from in your script is correct. When extracting data from midas, almost the same function is used in all the scripts: (the code pasted here actually is for extracting data from two different channels - ch 9 and ch 1)
```
def extract_data(fpath, max_events=None):
    mfile = midas.file_reader.MidasFile(fpath, use_numpy=True)
    
    leading_edges_ch1 = []
    leading_edges_ch2 = []
    trailing_edges_ch2 = []
    
    event_count = 0

    for event in mfile:
        if event.header.is_midas_internal_event():
            continue

        for bank_name in event.banks:
            bank = event.banks[bank_name]
            data = bank.data
            index = 0

            while index < len(data):
                trigger_timestamp = data[index]
                trigger_id = int(data[index + 1])
                header_word2 = int(data[index + 2])
                number_of_hits = header_word2 & 0xFFFFFFFF

                index += 3

                for _ in range(number_of_hits):
                    hit_word = int(data[index])
                    channel_id, edge, tot, toa = parse_hit_word(hit_word)
                    ts_ps = adc2time(toa) + trigger_timestamp * 12800

                    if channel_id == 1 and edge == 1:  # leading edge channel 1
                        leading_edges_ch1.append(ts_ps)
                    elif channel_id == 9:
                        if edge == 1:  # leading edge channel 2
                            leading_edges_ch2.append(ts_ps)
                        elif edge == 0:  # trailing edge channel 2
                            trailing_edges_ch2.append(ts_ps)

                    index += 1
        
        event_count += 1
        if max_events and event_count >= max_events:
            break

    return np.array(leading_edges_ch1), np.array(leading_edges_ch2), np.array(trailing_edges_ch2)

```
Make note of the fact that `channel_id` is the variable you would want to look at for changing channel ID.
Also most of the code scripts have an option to change the range of the data that the histogram is plotting, so those are changeable as well.

**Specific information about the Single Photon Timing Resolution (SPTR) plots**


Here is the code block where use input is required:

```
# Set the path to your MIDAS file
fpath = "C:/Users/Lenovo/Documents/TRIUMF General Fusion/run00035.mid"

# Set the maximum number of events to process (adjust as needed)
max_events = 100000  # Adjust this number to reduce processing time

# Define ToT range for filtering
tot_min = 2000  # Define your ToT minimum value for filtering
tot_max = 4700  # Define your ToT maximum value for filtering

# Define ToT bounds for the last plot
tot_lower_bound = 1085
tot_upper_bound = 1190

# Define difference range for filtering
diff_min = 10000  # Define your minimum difference value for filtering
diff_max = 11000  # Define your maximum difference value for filtering

# Define peak range for Gaussian fitting
peak_min = 150 # Set the minimum value for the peak range
peak_max = 200# Set the maximum value for the peak range
```
Most of these inputs are straightforward, here is clarification for a few:
- **tot_min / tot_max** : This is irrelevant, so feel free to ignore this.
- **tot_lower_bound/ tot_upper_bound** : this is where you actually define the ToT range to be filtered, before finding and plotting the differences
- diff_min/diff_max: This is for plotting the final "time differences between laser and SiPM" plot. You can specify the range to see a "zoomed-in" or "oomed-out" version of the plot.
- peak_min/ peak_min: This is for the gaussian curve-fit parameters
