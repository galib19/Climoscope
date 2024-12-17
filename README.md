# Climoscope
# Deep Learning Model Name

Potential names of the proposed deep learning models: 
- ClimateScape or FutureScape – Implies a model that paints a detailed "landscape" of future climate scenarios and extremes.
- Climoscope – Implies a comprehensive, deep look into future climates and their extremes.
- Suggest More/Select One


# Project README

## Important Note
Before making any changes, overwriting, or retraining any models, please create a copy of the codebase on the DMiner machine. **Do not include the `data/raw` folder**, as it is large. This way, if anything goes wrong, you can easily revert to the copied codebase. Additionally, this will help me track changes more effectively. Unfortunately, due to my current location in Taiwan, I am unable to create a copy on the DMiner machine.

## Folder Structure
- **data**
    - **raw**: Contains all the raw data (ERA5, GCMs).
    - **processed**: Contains all processed data derived from raw data.
    - **generated**: Contains all types of generated data using generative models.
    - **test**, **visualization**, and **old**: Contains EDA visualizations, outdated, and discarded data.
    - All other individual Python scripts (refer to the usage section below).

- **generative_modeling**: Contains all the generative modeling part
- **graphcast**: Contains all attempts to run Graphcast. Results are poor; consider using an alternative robust implementation or verify the current one.
- **representation_learning**: Contains all the representation learning part

## Usage
The DMiner machine has a shared structure for CUDA-based installations. For smooth execution of scripts without handling library installations, run commands inside the `myenv` virtual environment: conda activate myenv

(Note: Admin access may be required to create this environment under your username.)

### 1. Data Preprocessing

#### 1.1 ERA5 Data Preprocessing
1. **Run `data/era_processing_tas.py`**: This script processes the raw ERA5 data into NumPy format.  
   **Output**: `ERA5_tas_1980_2014_processed.npy`

2. **Block Maxima Processing**:
    - **Run `era5_processing_tas_block_maxima_sliding_window.py`**: Takes input from `ERA5_tas_1980_2014_processed.npy` and creates block maxima data using a sliding window by one day.  
      **Output**: `ERA5_tas_1980_2014_processed_block_maxima.npy`
      
    - **Run `era5_processing_tas_block_maxima_monthly.py`**: Takes input from `ERA5_tas_1980_2014_processed.npy` and creates block maxima data using a sliding window by typical month basis.  
      **Output**: `ERA5_tas_1980_2014_monthly_processed_block_maxima.npy`

#### 1.2 GCM Data Preprocessing
1. **Run `data/gcm_processing_tas.py`**: This script processes the raw GCM data into NumPy format.  
   **Output**: `GCM_{model_name}_tas_1980_2014_processed.npy`

2. **Block Maxima Processing**:
    - **Run `gcm_processing_tas_block_maxima_sliding_window.py`**: Takes input from `GCM_{model_name}_tas_1980_2014_processed.npy` and creates block maxima data using a sliding window by one day.  
      **Output**: `GCM_{model_name}_tas_1980_2014_processed_block_maxima.npy`
      
    - **Run `gcm_processing_tas_block_maxima_monthly_historical.py`**: Takes input from `GCM_{model_name}_tas_1980_2014_processed.npy` and creates block maxima data sliding window by typical month basis for historical evaluation.  
      **Output**: `GCM_{model_name}_tas_1980_2014_monthly_processed_block_maxima.npy`
      
    - **Run `gcm_processing_tas_block_maxima_monthly_future.py`**: Takes raw future GCM data and creates block maxima data for future evaluation.  
      **Output**: `GCM_{model_name}_tas_2025_2100_processed_block_maxima.npy`

### 2. Representation Learning
1. **Run `era5_representation_learning_v2.py`**: Takes input from `ERA5_tas_1980_2014_processed_block_maxima.npy` and performs representation learning (autoencoder) using the model defined in `representation_learning/autoencoder_model_ERA5.py`.  
   This script will automatically train, save results, visualize outputs, and store the trained autoencoder for use in generative modeling.

2. **Run `gcm_GEV_representation_learning.py`**: Takes input from `GCM_CESM2_tas_1980_2014_processed_block_maxima.npy` and performs representation learning (autoencoder) using the model defined in `representation_learning/autoencoder_model_GCM_GEV.py`.  
   Similar to above, this will train, save results, visualize outputs, and store the trained autoencoder.

3. Other scripts in the representation learning folder explore variations of the above two schemes (e.g., GEV vs non-GEV) which may yield sub-optimal results.

> Note: The main output from this section is two autoencoders (one for ERA5 and another for GCM), which will be extensively used in generative modeling parts. The trained autoencoders will be saved under the `trained_models` directory.

### 3. Generative Modeling
1. **Run `v4_conditional_training_tuning.py`**: This script trains the latest/current generative model using the model defined in `generative_modeling/v4_conditional_diffusion_model.py`. If you prefer not to retrain, you can use the currently trained model located at `generative_modeling/v4_conditional_diffusion_model.pth`.

2. Generate climate data:
    - **Run `conditional_generation_monthly_future.py`** for future climate data generation.
    - **Run `conditional_generation_monthly_historical.py`** for historical climate data generation.

3. (Optional) Use:
    - **`conditional_generation_monthly_future_GCM_reconstructed.py`**
    - **`conditional_generation_monthly_historical_GCM_reconstructed.py`**
    
   These scripts generate GCM reconstructed data for future and historical periods respectively. (Refer to the line plots)

4. To visualize results:
   - Run **`line_plot_GCM_ERA5.py`** to plot all generated mean data along with input data.

> Note: All intermediate figures (KDE, training loss, etc.) can be found under the `figures` directory. The old folder contains numerous variations/previous iterations of the current model (at least 20). None of these should be discarded as they have not been exhaustively tested.
