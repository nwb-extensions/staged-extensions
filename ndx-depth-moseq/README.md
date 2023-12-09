# ndx-depth-moseq Extension for NWB

ndx-depth-moseq is a standardized format for storing the output of [depth-moseq](https://dattalab.github.io/moseq2-website/index.html), an automatic motion sequencing algorithm, in NWB.  Currently, this extension only supports the output of depth-moseq-extract, but will be extended as needed to cover the other types of depth-moseq outputs.

This extension consists of 3 new neurodata types:

- `DepthImageSeries`, which is a simple extension of `pynwb.image.ImageSeries` for depth video with a constant reference depth.
- `MoSeqExtractParameterGroup`, which stores all the various parameters from the depth-moseq-extract algorithm.
- `MoSeqExtractGroup`, which stores all the relevant depth-moseq outputs including the `DepthImageSeries`, `MoSeqExtractParameterGroup`, as well as various native neurodata types such as the `Position`.

## Installation
```
pip install ndx-depth-moseq
```

## Usage

```python
"""Example of usage with mock data."""
from datetime import datetime
from pytz import timezone
import numpy as np
from pynwb.image import GrayscaleImage, ImageMaskSeries
from pynwb import NWBFile, TimeSeries
from pynwb.behavior import (
    CompassDirection,
    Position,
    SpatialSeries,
)
from ndx_depth_moseq import DepthImageSeries, MoSeqExtractGroup, MoSeqExtractParameterGroup

# Define mock data (this will be replaced with the actual data) 
version = "0.1.0"
num_frames = 10
num_rows = 512
num_cols = 424
processed_depth_video = np.zeros((num_frames, num_rows, num_cols))
loglikelihood_video = np.zeros((num_frames, num_rows, num_cols))
timestamps = np.arange(num_frames)
background = np.zeros((num_rows, num_cols))
is_flipped = np.zeros(num_frames, dtype=bool)
roi = np.zeros((num_rows, num_cols))
true_depth = 1.0
kinematic_var_names = ['centroid_x_mm', 'centroid_y_mm', 'height_ave_mm', 'angle', 'velocity_2d_mm', 'velocity_3d_mm', 'velocity_theta', 'length_mm', 'width_mm', 'area_px', 'width_px', 'length_px']
kinematic_vars = {k: np.zeros(num_frames) for k in kinematic_var_names}
kinematic_vars['length_px'] += 1
kinematic_vars['width_px'] += 1
parameters = {
    'angle_hampel_sig': np.array([3], dtype=np.int64)[0],
    'angle_hampel_span': np.array([5], dtype=np.int64)[0],
    'bg_roi_depth_range_min': np.array([0], dtype=np.int64)[0],
    'bg_roi_depth_range_max': np.array([1000], dtype=np.int64)[0],
    'bg_roi_dilate_x': np.array([10], dtype=np.int64)[0],
    'bg_roi_dilate_y': np.array([10], dtype=np.int64)[0],
    'bg_roi_fill_holes': True,
    'bg_roi_gradient_filter': True,
    'bg_roi_gradient_kernel': np.array([5], dtype=np.int64)[0],
    'bg_roi_gradient_threshold': np.array([10], dtype=np.int64)[0],
    'bg_roi_index': np.array([0], dtype=np.int64)[0],
    'bg_roi_shape': 'ellipse',
    'bg_roi_weight_area': np.array([0.5], dtype=np.float64)[0],
    'bg_roi_weight_extent': np.array([0.5], dtype=np.float64)[0],
    'bg_roi_weight_dist': np.array([0.5], dtype=np.float64)[0],
    'cable_filter_iters': np.array([5], dtype=np.int64)[0],
    'cable_filter_shape': 'ellipse',
    'cable_filter_size_x': np.array([5], dtype=np.int64)[0],
    'cable_filter_size_y': np.array([5], dtype=np.int64)[0],
    'centroid_hampel_sig': np.array([3], dtype=np.int64)[0],
    'centroid_hampel_span': np.array([5], dtype=np.int64)[0],
    'chunk_overlap': np.array([0], dtype=np.int64)[0],
    'chunk_size': np.array([100], dtype=np.int64)[0],
    'compress': False,
    'compress_chunk_size': np.array([100], dtype=np.int64)[0],
    'compress_threads': np.array([1], dtype=np.int64)[0],
    'config_file': 'config.yaml',
    'crop_size_width': np.array([512], dtype=np.int64)[0],
    'crop_size_height': np.array([424], dtype=np.int64)[0],
    'flip_classifier': 'flip_classifier.pkl',
    'flip_classifier_smoothing': np.array([5], dtype=np.int64)[0],
    'fps': np.array([30], dtype=np.int64)[0],
    'frame_dtype': 'uint16',
    'frame_trim_beginning': np.array([0], dtype=np.int64)[0],
    'frame_trim_end': np.array([0], dtype=np.int64)[0],
    'max_height': np.array([1000], dtype=np.int64)[0],
    'min_height': np.array([0], dtype=np.int64)[0],
    'model_smoothing_clips_x': np.array([5], dtype=np.int64)[0],
    'model_smoothing_clips_y': np.array([5], dtype=np.int64)[0],
    'spatial_filter_size': np.array([5], dtype=np.int64)[0],
    'tail_filter_iters': np.array([5], dtype=np.int64)[0],
    'tail_filter_shape': 'ellipse',
    'tail_filter_size_x': np.array([5], dtype=np.int64)[0],
    'tail_filter_size_y': np.array([5], dtype=np.int64)[0],
    'temporal_filter_size': np.array([5], dtype=np.int64)[0],
    'tracking_model_init': 'mean',
    'tracking_model_ll_clip': np.array([5], dtype=np.int64)[0],
    'tracking_model_ll_threshold': np.array([5], dtype=np.int64)[0],
    'tracking_model_mask_threshold': np.array([5], dtype=np.int64)[0],
    'tracking_model_segment': True,
    'use_plane_bground': True,
    'use_tracking_model': True,
    'write_movie': False,
}

# Create the NWB file
nwbfile = NWBFile(
    session_description="session_description",
    identifier="identifier",
    session_start_time=datetime.now(timezone("US/Pacific")),
)

# Add Imaging Data
kinect = nwbfile.create_device(name="kinect", manufacturer="Microsoft", description="Microsoft Kinect 2")
flipped_series = TimeSeries(
    name="flipped_series",
    data=is_flipped,
    unit="a.u.",
    timestamps=timestamps,
    description="Boolean array indicating whether the image was flipped left/right",
)
processed_depth_video = DepthImageSeries(
    name="processed_depth_video",
    data=processed_depth_video,
    unit="millimeters",
    format="raw",
    timestamps=flipped_series.timestamps,
    description="3D array of depth frames (nframes x w x h, in mm)",
    distant_depth=true_depth,
    device=kinect,
)
loglikelihood_video = ImageMaskSeries(
    name="loglikelihood_video",
    data=loglikelihood_video,
    masked_imageseries=processed_depth_video,
    unit="a.u.",
    format="raw",
    timestamps=flipped_series.timestamps,
    description="Log-likelihood values from the tracking model (nframes x w x h)",
    device=kinect,
)
background = GrayscaleImage(
    name="background",
    data=background,
    description="Computed background image.",
)
roi = GrayscaleImage(
    name="roi",
    data=roi,
    description="Computed region of interest.",
)

# Add Position Data
position_data = np.vstack(
    (kinematic_vars["centroid_x_mm"], kinematic_vars["centroid_y_mm"], kinematic_vars["height_ave_mm"])
).T
position_series = SpatialSeries(
    name="position",
    description="Position (x, y, height) in an open field.",
    data=position_data,
    timestamps=flipped_series.timestamps,
    reference_frame="top left",
    unit="mm",
)
position = Position(spatial_series=position_series, name="position")

# Add Compass Direction Data
heading_2d_series = SpatialSeries(
    name="heading_2d",
    description="Head orientation.",
    data=kinematic_vars["angle"],
    timestamps=flipped_series.timestamps,
    reference_frame="top left",
    unit="radians",
)
heading_2d = CompassDirection(spatial_series=heading_2d_series, name="heading_2d")

# Add speed/velocity data
speed_2d = TimeSeries(
    name="speed_2d",
    description="2D speed (mm / frame), note that missing frames are not accounted for",
    data=kinematic_vars["velocity_2d_mm"],
    timestamps=flipped_series.timestamps,
    unit="mm/frame",
)
speed_3d = TimeSeries(
    name="speed_3d",
    description="3D speed (mm / frame), note that missing frames are not accounted for",
    data=kinematic_vars["velocity_3d_mm"],
    timestamps=flipped_series.timestamps,
    unit="mm/frame",
)
angular_velocity_2d = TimeSeries(
    name="angular_velocity_2d",
    description="Angular component of velocity (arctan(vel_x, vel_y))",
    data=kinematic_vars["velocity_theta"],
    timestamps=flipped_series.timestamps,
    unit="radians/frame",
)

# Add length/width/area data
length = TimeSeries(
    name="length",
    description="Length of mouse (mm)",
    data=kinematic_vars["length_mm"],
    timestamps=flipped_series.timestamps,
    unit="mm",
)
width = TimeSeries(
    name="width",
    description="Width of mouse (mm)",
    data=kinematic_vars["width_mm"],
    timestamps=flipped_series.timestamps,
    unit="mm",
)
width_px_to_mm = kinematic_vars["width_mm"] / kinematic_vars["width_px"]
length_px_to_mm = kinematic_vars["length_mm"] / kinematic_vars["length_px"]
area_px_to_mm2 = width_px_to_mm * length_px_to_mm
area_mm2 = kinematic_vars["area_px"] * area_px_to_mm2
area = TimeSeries(
    name="area",
    description="Pixel-wise area of mouse (mm^2)",
    data=area_mm2,
    timestamps=flipped_series.timestamps,
    unit="mm^2",
)

# Add Parameters
parameters = MoSeqExtractParameterGroup(name="parameters", **parameters)

# Add MoseqExtractGroup
moseq_extract_group = MoSeqExtractGroup(
    name="moseq_extract_group",
    version=version,
    parameters=parameters,
    background=background,
    processed_depth_video=processed_depth_video,
    loglikelihood_video=loglikelihood_video,
    roi=roi,
    flipped_series=flipped_series,
    depth_camera=kinect,
    position=position,
    heading_2d=heading_2d,
    speed_2d=speed_2d,
    speed_3d=speed_3d,
    angular_velocity_2d=angular_velocity_2d,
    length=length,
    width=width,
    area=area,
)
# Add data into a behavioral processing module
behavior_module = nwbfile.create_processing_module(
    name="behavior",
    description="Processed behavioral data from MoSeq",
)
behavior_module.add(moseq_extract_group)
```

---
This extension was created using [ndx-template](https://github.com/nwb-extensions/ndx-template).
