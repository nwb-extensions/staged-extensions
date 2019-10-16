# ndx-ecog Extension for NWB:N

Author: Ben Dichter

There are three data types, `Surface`, `CorticalSurfaces`, and `ECoGSubject`. `CorticalSurfaces` is simply a group (like a folder) to put `Surface` objects into. `Surface` holds surface mesh data (vertices and triangular faces) for sections of cortex. `ECoGSubject` is an extension of `Subject` that allows you to add the `CorticalSurfaces` object to `/general/subject`.

## Usage

### python

install:
```bash
pip install ndx_ecog
```

write:
```python
import pynwb
from ndx_ecog import CorticalSurfaces, ECoGSubject

nwbfile = pynwb.NWBFile(...)

...

cortical_surfaces = CorticalSurfaces()
## loop me
    cortical_surfaces.create_surface(name=name, faces=faces, vertices=veritices)
##
nwbfile.subject = ECoGSubject(cortical_surfaces=cortical_surfaces)
```

You can optionally attach images of the subject's brain:
```python
from pynwb.base import Images
from pynwb.image import GrayscaleImage

subject.images = Images(name='subject images', images=[GrayscaleImage('image1', data=image_data)])
```

read:
```python
import nwbext_ecog
from pynwb import NWBHDF5IO
io = NWBHDF5IO('path_to_file.nwb','r')
nwb = io.read()
nwb.subject.cortical_surfaces
```

### MATLAB
install:
```matlab
generateExtension('/path/to/ndx-ecog/spec/ndx-ecog.namespace.yaml');
```

write:
```matlab
cortical_surfaces = types.ecog.CorticalSurfaces;

%%% loop me
    surf = types.ecog.Surface('faces', faces, 'vertices', vertices);
    cortical_surfaces.surface.set(surface_name, surf);
%%%

file.subject = types.ecog.ECoGSubject(name, cortical_surfaces);
```
