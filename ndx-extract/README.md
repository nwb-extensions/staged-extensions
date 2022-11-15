# ndx-extract Extension for NWB

Author: Cesar Echavarria

This extension allows for the storage of configuration options used by the [EXTRACT](https://github.com/schnitzer-lab/EXTRACT-public) tool for calcium imaging.


## Usage


### Python
Install the extension from [PyPI](https://pypi.org/project/ndx-extract/)
```shell
pip install ndx-extract
```
Usage:
```python
from datetime import datetime
from ndx_extract import EXTRACTSegmentation
from pynwb import NWBFile, NWBHDF5IO

# Create the NWBfile
nwbfile = NWBFile(
    session_description="The mouse in open exploration.",
    identifier="Mouse5_Day3",
    session_start_time=datetime.now().astimezone(),
)
# Create the processing module
ophys_module = nwbfile.create_processing_module(
    name="ophys",
    description="optical physiology processed data",
)
# Create the segmentation object and define the configuration properties
# The properties that can be defined are listed at spec/ndx-EXTRACT.extensions.yaml
image_segmentation = EXTRACTSegmentation(
            name="ImageSegmentation",
            version="1.1.0",
            preprocess=True,
            trace_output_option="nonneg",
)
# Add this image segmentation to the processing module
ophys_module.add(image_segmentation)

# Writing the NWB file
with NWBHDF5IO("image_segmentation.nwb", mode="w") as io:
    io.write(nwbfile)

# Reading the NWB file and accessing the segmentation parameters
with NWBHDF5IO("image_segmentation.nwb", mode="r") as io:
    nwbfile_in = io.read()
    nwbfile_in.processing["ophys"].data_interfaces["ImageSegmentation"].version
    nwbfile_in.processing["ophys"].data_interfaces["ImageSegmentation"].preprocess
    nwbfile_in.processing["ophys"].data_interfaces["ImageSegmentation"].trace_output_option
```

Running the tests:
```shell
 python -m unittest src/pynwb/tests/test_extract.py
```

### MATLAB
install:
```matlab
generateExtension('/path/to/ndx-extract/spec/ndx-extract.namespace.yaml');
```

write:
```matlab
% define NWB file
nwb = NwbFile( ...
    'session_description', 'mouse in open exploration', ...
    'identifier', 'Mouse5_Day3', ...
    'session_start_time', datetime(2018, 4, 25, 2, 30, 3) ...
);
% define processing module
ophys_module = types.core.ProcessingModule( ...
    'description', 'test processing module' ...
);
nwb.processing.set('ophys', ophys_module);
% define segmentation
img_seg = types.ndx_extract.EXTRACTSegmentation();
% set segmentation properties
img_seg.trace_output_option = 'nonneg';
img_seg.save_all_found = false;
img_seg.dendrite_aware = false;
img_seg.adaptive_kappa = false;
img_seg.use_sparse_arrays = false;
img_seg.dendrite_aware = 0;
img_seg.hyperparameter_tuning_flag = false;
img_seg.remove_duplicate_cells = false;
img_seg.max_iter = 6;
img_seg.S_init = rand(100,10);
img_seg.T_init = rand(100,10);
img_seg.preprocess = true;
img_seg.fix_zero_FOV_strips = false;
img_seg.medfilt_outlier_pixels = false;
img_seg.skip_dff = false;
img_seg.baseline_quantile = .4;
img_seg.skip_highpass = false;
img_seg.spatial_highpass_cutoff = 0;
img_seg.temporal_denoising = false;
img_seg.remove_background = true;
img_seg.cellfind_filter_type = 'butter';
img_seg.spatial_lowpass_cutoff = 2;
img_seg.moving_radius = 3;
img_seg.cellfind_min_snr = 1;
img_seg.cellfind_max_steps = 1000;
img_seg.cellfind_kappa_std_ratio = 1;
img_seg.init_with_gaussian = false;
img_seg.kappa_std_ratio = 1;
img_seg.downsample_time_by = 'auto';
img_seg.downsample_space_by = 'auto';
img_seg.min_radius_after_downsampling = 5;
img_seg.min_tau_after_downsampling = 5;
img_seg.reestimate_S_if_downsampled = false;
img_seg.reestimate_T_if_downsampled = true;
img_seg.crop_circular = false;
img_seg.movie_mask = randi(2,100,100)-1;
img_seg.smoothing_ratio_x2y = 0;
img_seg.compact_output = true;
img_seg.cellfind_numpix_threshold = 9;
img_seg.high2low_brightness_ratio = Inf;
img_seg.l1_penalty_factor = 0;
img_seg.T_lower_snr_threshold = 10;
img_seg.smooth_T = false;
img_seg.smooth_S = true;
img_seg.max_iter_S = 100;
img_seg.max_iter_T = 100;
img_seg.TOL_sub = 1.0000e-06;
img_seg.TOL_main = 0.0100;
img_seg.avg_cell_radius = 0;
img_seg.T_min_snr = 10;
img_seg.size_lower_limit = .1000;
img_seg.size_upper_limit = 10;
img_seg.temporal_corrupt_thresh = 0.7000;
img_seg.spatial_corrupt_thresh = 0.7000;
img_seg.eccent_thresh = 6;
img_seg.low_ST_index_thresh = 0.0100;
img_seg.low_ST_corr_thresh = 0;
img_seg.S_dup_corr_thresh = 0.9500;
img_seg.T_dup_corr_thresh = 0.9500;
img_seg.confidence_thresh = 0.8000;
img_seg.high_ST_index_thresh = 0.8000;
ophys_module.nwbdatainterface.set('ImgSegmentation', img_seg);
nwbExport(nwb, 'test_123.nwb');
```

run tests:
```matlab
cd /path/to/ndx-extract/src/matnwb/tests
results = test_ndx_extract()
```
