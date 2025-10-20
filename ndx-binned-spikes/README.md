# ndx-binned-spikes Extension for NWB

This extension is meant for storing binned spike count data from neural recordings. It provides two types: `BinnedAlignedSpikes` for storing spike counts aligned to specific events (like stimuli or behavioral events) in a 3D structure (units × events × bins), ideal for peri-stimulus time histogram (PSTH) analysis; and `BinnedSpikes` for storing continuous spike counts across entire experimental sessions in a 2D structure (units × bins), useful for analyzing long-term neural activity patterns. Both classes support linking to NWB Units tables and can handle multiple units simultaneously, making it easy to store and analyze pre-binned spike data without recomputing from raw spike times.

See the full description and documentation at:
https://github.com/catalystneuro/ndx-binned-spikes

