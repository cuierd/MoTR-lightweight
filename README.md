# Mouse Tracking for Reading (MoTR)
This repository contains a lightweight version of "Mouse Tracking for Reading (MoTR)".
There are three experiments:
1. A demo experiment of naturalistic reading using Provo corpus (Luke & Christianson, 2018). It is in the folder `multilingual_motr/demo-provo`.

- This experiment use a wider screen so that longer sentences can be displayed in one line. Please make sure your screen is wide enough (e.g. a laptop screen). 

2. An word masking experiment to study the information distribution in English (Ding et al., 2025) (https://arxiv.org/pdf/2509.17047) . It is in the folder `multilingual_motr/en`.
3. An word masking experiment to study the information distribution in Chinese (Ding et al., 2025) (https://arxiv.org/pdf/2509.17047). It is in the folder `multilingual_motr/zh`.

### Demo
Provo demo experiment: [link](https://cuierd.github.io/MoTR-lightweight/multilingual_motr/demo-provo/).
Chinese word masking experiment: [link](https://cuierd.github.io/MoTR-lightweight/multilingual_motr/zh/).
English word masking experiment: [link](https://cuierd.github.io/MoTR-lightweight/multilingual_motr/en/).

### Running MoTR in Magpie

#### Contents
- `demo-provo` contains the  codes (in subdirectory `src`) and the trial data (in subdirectory `trials`) for running a MoTR experiment for Provo.
- `zh` contains the codes (in subdirectory `src`) and the trial data (in subdirectory `trials`) for running a MoTR experiment for Chinese word masking.
- `en` contains the codes (in subdirectory `src`) and the trial data (in subdirectory `trials`) for running a MoTR experiment for English word masking.

#### Workflow for Running a Basic MoTR Trial

The following workflow should enable you to run a MoTR experiment, locally, in your browser.

- Download Node.js v16.20.2 (in this [link](https://nodejs.org/en/download/releases)) and install.
- Clone this repository.
- Open a local terminal or IDE, navigate to MoTR/MoTR/run_motr_in_magpie/provo/ (or MoTR/MoTR/run_motr_in_magpie/attachment/).
- Run ```sh update_modules.sh```. This will use our lightened version of Magpie-base.
- If you want to use the default Magpie-base, instead of the last step, run ```npm install```  and then ```npm run serve```.
- Click either of the links shown in the terminal starting with "-Local:" or "-Network:".
- Enjoy!

For more information about how to deploy Magpie experiments onto the web, and run them with human participants, please see the [Magpie documentation](https://magpie-experiments.org/). A simple workflow is in folder `resources/Workflow-MoTR-with-notes.pdf`.

### Post Processing

Please note that you need to adapt this pipeline to your own experiment.

#### Contents
- `postprocessing.py` is the main script to run which will take the command line interface arguments and post process the MoTR data.
- `utils` contains modules which will be used by `postprocessing.py`.
  - `divideCsv.py` can divide the large raw MoTR data file into separate files for different subjects, each containing their respective reading data. We assume you will get a csv file containing the data for multiple participants by running a MoTR experiment. 
  - `preprocessTrialData.py` preprocesses the trial data file by splitting sentences into words and extracting relevant information from these files. We assume that the items in your trial data are sentences, paragraphs or passages, rather than words. Otherwise, you don't need this step.
  - `mergeFixations.py` takes the divided raw data files and merges the adjacent data points into fixations if they are on the same words. It also filters out noise.
  - `extractLingusticFeatures.py` computes reading measures from fixations, including Gaze Duration, Total Duration, Go Past Time, First-Pass Regression Probability etc.
  - `tsv_csv_json_convert.py`  (specific to our experiments) is the script we used to convert the original trial data from Witzel et al., (2012) and Boyce et al., (2020) to a csv/tsv file which can then be used as input to magpie.
  - `trial_splits.py` is the script for breaking the large number of items into sub experiments for our attachment preference study, ensuring that same item under different conditions will not appear in the same sub experiment.

#### Running the Post Processing Pipeline

- Prepare your raw MoTR data. You can also access our raw data from this experiment, please [click on this link](https://osf.io/4g7pr/?view_only=d628c9fda891434bb9ad4aa13b5d21ab). 
  - Data can be a single MoTR reading data file or a folder containing multiple files. 
- Prepare your trial data.
  - Data can either be in its original form or preprocessed, with sentences already split into words. 
- Run the `postprocessing.py` script and specify customized paths for the resulting divided raw data, fixations, reading measures, and preprocessed trial data. 
  - If you do not specify your paths, they will be placed in the default directories (`./divided`,`./fixations`, `./reading_measures`, `./processed_trial`, respectively ). It is possible to set your own lower boundary and upper boundary to define a fixation. If not specified, it is **160 ms** and **4000 ms**, by default. We suggest that you check the sampling rate in the `experiments` folder before choosing the boundaries. For our two experiments, the sampling rate is **40 HZ**.
  - We provide some sample calls bellow.

 ```bash
 python3 postprocessing.py --in_file local_coherence_reading_data.csv --trial_file trials/localCoherence.tsv
 ```

 This call takes a raw MoTR data file called `local_coherence_reading_data.csv` and the trial item data `localCoherence.tsv` in the folder `trials`, output the divided raw data files, files for fixation sequences, files for reading measures and processed trial data file into `./divided`,`./fixations`, `./reading_measures`, `./processed_trial`, respectively.

 ```bash
 python3 postprocessing.py --in_file local_coherence_reading_data.csv --processed_trial_file trials/localCoherence_sent_region.csv
 ```

This call takes a raw MoTR data file called `local_coherence_reading_data.csv` and the already processed trial data `localCoherence_sent_region.csv` in the folder `trials`, output the divided raw data files, files for fixation sequences, files for reading measures into `./divided`,`./fixations`, `./reading_measures`, respectively.

```bash
python3 postprocessing.py --in_folder data --processed_trial_file trials/localCoherence_sent_region.csv
```

This call takes a folder which can contain multiple raw MoTR data files called `data` and the already processed trial data `localCoherence_sent_region.csv` in the folder `trials`, output the divided raw data files, files for fixation sequences, files for reading measures into `./divided`,`./fixations`, `./reading_measures`, respectively.

```bash
python3 postprocessing.py --in_file provo_reading_data.csv --trial_file trials/provo_items.tsv --divided_dir divide_by_reader --processed_trial_dir ../stimuli --fixation_dir fixations --rt_dir reading_metrics --low_thres 200 --up_thres 3000
```

This call takes a raw MoTR data file called `provo_reading_data.csv` and the trial item data `provo_items.tsv` in the folder `trials`, output the divided raw data files, files for fixation sequences, files for reading measures and processed trial data file into `./divide_by_reader`,`./fixations`, `./reading_metrics`, `../stimuli`, respectively. It will extract fixations ranging from 200 ms to 3000 ms.

```bash
python3 postprocessing.py --in_folder data2 --trial_file trials/provo_items.tsv --divided_dir divide_by_reader --fixation_dir fixations --rt_dir reading_metrics --low_thres 200 --up_thres 3000
```

This call takes a folder which can contain multiple raw MoTR data files called `data2` and the trial data file `provo_items.tsv` in the folder `trials`, output the divided raw data files, files for fixation sequences, files for reading measures into `./divide_by_reader`,`./fixations`, `./reading_metrics`, `../stimuli`, respectively. It will extract fixations ranging from 200 ms to 3000 ms.

