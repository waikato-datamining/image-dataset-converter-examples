Readers and writers for image segmentation have the `-is` suffix.

# Blue channel to Indexed PNG

Download the blue channel archive of the [camvid](https://datasets.cms.waikato.ac.nz/ufdl/camvid/) dataset
and extract it.

The following command-line will convert it into a dataset using indexed PNG files:

```bash
idc-convert \
  -l INFO \
  from-blue-channel-is \
    -l INFO \
    -i ./bluechannel/*.png
    --labels Animal Archway Bicyclist Bridge Building Car CartLuggagePram Child Column_Pole \
             Fence LaneMkgsDriv LaneMkgsNonDriv Misc_Text MotorcycleScooter OtherMoving ParkingBlock \
             Pedestrian Road RoadShoulder Sidewalk SignSymbol Sky SUVPickupTruck TrafficCone \
             TrafficLight Train Tree Truck_Bus Tunnel VegetationMisc Void Wall \
  to-indexed-png-is \
    -l INFO \
    -p x11 \
    -o ./indexedpng
```

**NB:** Uses the [X11 color palette](https://en.wikipedia.org/wiki/X11_color_names) for the palette in the PNGs.

