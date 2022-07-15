# LeahyAnalysis
General workflow:
Raw data had to be downloaded using academic torrents; since only subject P01's results were inlcuded in report, I only included their raw data (under \OpenMIIR-RawEEG_v1).

exploratory analysis + ICA ocular artifact removal using MNE module + jupyter notebooks in 'Notebooks' folder
(note: these require ipykernels to run on an MNE envirnment see: https://mne.tools/stable/install/advanced.html)
Preprocessing: 
1. re-reference to average
2.  bandpass filter between 0.5 - 30 Hz
3.  conduct infomax ICA (without any dimensionality reduction ie all PCA components)
4.  evaluate component correlations with EOG channels and select which components to inlcude in demixing matrix
5.  Apply demixing to raw (pre-filter) data and export as fif file (\postICA_EOG_EEG\eeglab_handles), adn mat file including stim event info (\postICA_EOG_EEG\eeglab_handles\mne-outputs)

pre-processed individual subjects in separate notebooks (Although ultimately only results for subject P01 were included in report)

Raw data included EEG responses in 4 conditions 
(see Stober, Sebastian, et al. "Towards Music Imagery Information Retrieval: Introducing the OpenMIIR Dataset of EEG Recordings from Music Perception and Imagination." ISMIR. 2015. for details)
So the stim channel marked the start of a trial followed by a marker indicating the condition/stimulus of the trial w/o any duration information
Perception trials were preceded by 2 bars of cue clicks (to help subjects match tempo in subsequent imagination condition)
which had to be removed for analysis so I referenced the Stimuli_Metav1.xlsx in https://github.com/sstober/openmiir/tree/master/meta
to add cue + music duration for music within a trial and save it as an events file for epoching using EEGlab (under \postICA_EOG_EEG\eeglab_handles\mne-outputs)

all matlab scripts for analysis in foler 'mScripts'
in order used:
1. load_dependencies.m : (had put mTRF and EEGLAB folders under the Engineering analysis folder so I could edit/debug some of the functions and doing so in the MATLAB toolboxes folder is incompatible with Windows); unnecessary if EEGLAB ad mTRF already added to path
2. import_handle.m: import denoised files and event info files save as as EEGLAB sets under /postICA_EOG_EEG/eeglab_handles/sets-pre-filter
3. filter_convert_data.m : apply 1-8 Hz bandpass, save filtered data and events info as mat files under /postICA_EOG_EEG/eeglab_handles/filtered
4. extract_trials_features.m : function that epochs eeg data to stim onset and offset + 100ms padding after/before onset/offset and calculates features specified. EGG and features are downsampled to 128 Hz after antialising filter. Feature selection options include envelope (from hilbert transform ->abs val-), phase-randomized envelope (fft -> add random phases to non-dc coefficients ->ifft) beats (onsets included as text file from openmiir, helper function get_beats_slice.m used to find and resample)
5. beats_mTRF_subjectP01.mlx : live script used for evaluating adn debugging mTRF models on-line
6. comparisons.m: wrapper function for conducting nested-crossvalidation of models being compared (beats + envelope vs envelope + derivative in this case).
