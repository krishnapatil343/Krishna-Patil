# Water_Detection  :
**If you like the repo put a star on it!** :star: 

"Welcome to All! 🌊 We're dedicated to leveraging cutting-edge technology to detect water bodies using satellite imagery. Our focus is on environmental monitoring, disaster management, and sustainable resource utilization. Join us as we explore the beauty of Earth's landscapes and work towards a better, more water-conscious future.  

![Huallaga_River](https://github.com/krishnapatil343/Krishna-Patil/assets/121950565/f8ff0650-a488-4c7b-a63e-f4eade2eca2d)
![WhatsApp Image 2024-04-05 at 12 35 03 PM](https://github.com/krishnapatil343/Krishna-Patil/assets/121950565/a7a48e44-8609-454d-b0f9-dc5473cca41a) 
### Project description

Satellite remote sensing can provide low-cost long-term shoreline data capable of resolving the temporal scales of interest to Hydrological scientists and engineers at sites where no in-situ field measurements are available. Water_Detection enables the non-expert user to extract Riverline from Landsat 5, Landsat 7, Landsat 8, Landsat 9 and Sentinel-2 images.
The Water detection algorithm is optimised for sandy soil Aqua lines. It combines a sub-pixel border segmentation and an image classification component, which refines the segmentation into four distinct categories such that the water detection is specific to the sand/water interface.

The toolbox has the following functionalities:
1. easy retrieval of satellite imagery spanning the user-defined region of interest and time period from Google Earth Engine, including state-of-the-art pre-processing steps (re-projecting the different bands, pansharpening, advanced cloud masking).
2. automated extraction of shorelines from all the selected images using a sub-pixel resolution technique and options for quality-control.
### Table of Contents

- [Installation](#installation)
- [Usage](#usage)
   - [Retrieval of the satellite images in GEE](#retrieval)
   - [Shoreline detection](#detection) 
  
## 1. Installation<a name="introduction"></a>

### 1.1 Create an environment with Anaconda

To run the toolbox you first need to install the required Python packages in an environment. To do this we will use **Anaconda**, which can be downloaded freely [here](https://www.anaconda.com/download/). If you are a more advanced user and have **Mamba** installed, use Mamba as it will install everything faster and without problems (highly recommended if you know about python environments).

Once you have it installed on your PC, open the Anaconda prompt (in Mac and Linux, open a terminal window) and use the `cd` command (change directory) to go the folder where you have downloaded this repository (e.g., `cd C:\Users\kilian\Documents\Github\CoastSat`).

Create a new environment named `coastsat` with all the required packages by entering these commands in succession:

```
conda create -n waterdetection
conda activate waterdetection
conda install -c conda-forge geopandas -y
conda install -c conda-forge earthengine-api scikit-image matplotlib astropy notebook -y
pip install pyqt5 imageio-ffmpeg
```

All the required packages have now been installed and are self-contained in an environment called `waterdetection`. Always make sure that the environment is activated with:

```
conda activate waterdetection
```

To confirm that you have successfully activated waterdetection, your terminal command line prompt should now start with (waterdetection).

:warning: **In case errors are raised** :warning:: clean things up with the following command (better to have the Anaconda Prompt open as administrator) before attempting to install `waterdetection` again:
```
conda clean --all
```

You can also install the packages with the **Anaconda Navigator**, in the *Environments* tab. For more details, the following [link](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-with-commands) shows how to create and manage an environment with Anaconda.

### 1.2 Activate Google Earth Engine Python API

First, you need to request access to Google Earth Engine at https://signup.earthengine.google.com/. Then install `gcloud`, go to https://cloud.google.com/sdk/docs/install and install gcloud CLI. After you have installed gcloud CLI it will automatically launch and let you authenticate with your GEE account (or gmail). Then close the Anaconda Prompt and restart it.

Now you are ready to start using the waterdetection toolbox!

:warning: remember to always activate the environment with `conda activate waterdetection` each time you are preparing to use the toolbox.

## 2. Usage<a name="usage"></a>

An example of how to run the software in a Jupyter Notebook is provided in the repository (`waterdetection.ipynb`). To run this, first activate your `waterdetection` environment with `conda activate waterdetection` (if not already active), and then type:

```
jupyter notebook
```

A web browser window will open. Point to the directory where you downloaded this repository and click on `waterdetection.ipynb`. A Jupyter Notebook combines formatted text and code. To run the code, place your cursor inside one of the code sections and click on the `run cell` button (or press `Shift` + `Enter`) and progress forward.

<img width="1190" alt="Screenshot 2024-04-06 at 10 04 06" src="https://github.com/krishnapatil343/Krishna-Patil/assets/121950565/951678c3-88dc-4a56-b8fa-5937d8a4b5c8">

The following sections show an example of how to run the full water detection workflow at Huallaga-River (Peru).

### 2.1 Retrieval of the satellite images<a name="retrieval"></a>

To retrieve from the GEE server the available satellite images cropped around the user-defined region of coastline for the particular time period of interest, the following variables are required:
- `polygon`: the coordinates of the region of interest (longitude/latitude pairs in WGS84), do not exceed 100 sqkm.
- `dates`: dates over which the images will be retrieved (e.g., `dates = ['2017-12-01', '2018-01-01']`)
- `sat_list`: satellite missions to consider (e.g., `sat_list = ['L5', 'L7', 'L8', 'L9', 'S2']` for Landsat 5, 7, 8, 9 and Sentinel-2 collections)
- `sitename`: name of the site (this is the name of the subfolder where the images and other accompanying files will be stored)
- `filepath`: filepath to the directory where the data will be stored
- `landsat_collection`: whether to use Collection 1 (`C01`) or Collection 2 (`C02`). Note that after 2022/01/01, Landsat images are only available in Collection 2. Landsat 9 is therefore only available as Collection 2. So if the user has selected `C01`, images prior to 2022/01/01 will be downloaded from Collection 1, while images captured after that date will be automatically taken from `C02`.
- (optional) `S2tile`: for Sentinel-2 only, this parameter let's you specify the tile from which you'd like to crop your ROI, this avoids having many duplicates in the Sentinel-2 collection. 
The call `metadata = SDS_download.retrieve_images(inputs)` will launch the retrieval of the images and store them as .TIF files (under */filepath/sitename*). The metadata contains the exact time of acquisition (in UTC time) of each image, its projection and its geometric accuracy. If the images have already been downloaded previously and the user only wants to run the Riverline detection, the metadata can be loaded directly by running `metadata = SDS_download.get_metadata(inputs)`.

The screenshot below shows an example of inputs that will retrieve all the images of Huallaga-River (Peru) acquired by Sentinel-2.

<img width="1119" alt="Screenshot 2024-04-06 at 10 11 02" src="https://github.com/krishnapatil343/Krishna-Patil/assets/121950565/c13a97f9-dfb4-4f89-92ac-c386b00e002d">

:warning: The area of the polygon should not exceed 100 km2, so for very long beaches split it into multiple smaller polygons.

### 2.2 Riverline detection<a name="detection"></a>

To map the riverline, the following user-defined settings are needed:
- `cloud_thresh`: threshold on maximum cloud cover that is acceptable on the images (value between 0 and 1 - this may require some initial experimentation).
- `dist_clouds`: buffer around cloud pixels where Riverline is not mapped (in metres)
- `output_epsg`: epsg code defining the spatial reference system of the Riverline coordinates. It has to be a cartesian coordinate system (i.e. projected) and not a geographical coordinate system (in latitude and longitude angles). See http://spatialreference.org/ to find the EPSG number corresponding to your local coordinate system. If you do not use a local projection, your results may not be accurate.
- `check_detection`: If set to `True,` the user can quality control each riverline detection interactively (recommended when mapping riverlines for the first time) and accept/reject each riverline.
- `adjust_detection`: in case users want more control over the detected riverline, they can set this parameter to `True`, then they will be able to manually adjust the threshold used to map the riverline on each image.
- `save_figure`: if set to `True` a figure of each mapped shoreline is saved under */filepath/sitename/jpg_files/detection*, even if the two previous parameters are set to `False`. Note that this may slow down the process.

There are additional parameters (`min_beach_size`, `min_length_sl`, `cloud_mask_issue`, `sand_color` and `pan_off`) that can be tuned to optimise the riverline detection (for Advanced users only). For the moment leave these parameters set to their default values, we will see later how they can be modified.

An example of settings is provided here:

![image](https://user-images.githubusercontent.com/7217258/182158840-ef1c527c-6ddb-44ab-a6fc-f4b46c8b0127.png)

#### Timelapse animation in MP4

Once the images have been downloaded you can visualise them and create an MP4 animation using `SDS_preprocess.save_jpg(metadata, settings)` and `SDS_tools.make_animation_mp4(fp_images, fps, fn_animation)` as shown in the Jupyter Notebook.

![image](https://github.com/kvos/CoastSat/assets/7217258/5e45fa2d-221f-4c00-8ee9-e235e90a2ab8)


#### Reference Riverline [HIGHLY RECOMMENDED]

Before running the batch riverline detection, there is the option to manually digitize a reference riverline on one cloud-free image. This reference riverline helps to reject outliers and false detections when mapping riverlines as it only considers as valid riverlines the points that are within a defined distance from this reference riverlines (defined by `settings['max_dist_ref']`).

 The user can manually digitize one or several reference shorelines on one of the images by calling:
```
settings['reference_shoreline'] = SDS_preprocess.get_reference_sl_manual(metadata, settings)
settings['max_dist_ref'] = 100 # max distance (in meters) allowed from the reference riverlines
```
This function allows the user to click points along the shoreline on cloud-free satellite images, as shown in the animation below.

![ScreenRecording2024-04-06at10 20 26-ezgif com-video-to-gif-converter](https://github.com/krishnapatil343/Krishna-Patil/assets/121950565/242d6170-4a3a-4262-b29a-31b6f40122cc)

The maximum distance (in metres) allowed from the reference shoreline is defined by the parameter `max_dist_ref`. This parameter is set to a default value of 100 m. If you think that 100 m buffer from the reference shoreline will not capture the shoreline variability at your site, increase the value of this parameter. This may be the case for large nourishments or eroding/accreting coastlines.

#### Batch riverlines detection

Once all the settings have been defined, the batch riverline detection can be launched by calling:
```
output = SDS_shoreline.extract_shorelines(metadata, settings)
```
When `check_detection` is set to `True`, a figure like the one below appears and asks the user to manually accept/reject each detection by pressing **on the keyboard** the `right arrow` (⇨) to `keep` the riverline or `left arrow` (⇦) to `skip` the mapped shoreline. The user can break the loop at any time by pressing `escape` (nothing will be saved though).
When `adjust_detection` is set to `True`, a figure like the one below appears and the user can adjust the position of the riverline by clicking on the histogram of MNDWI pixel intensities. Once the threshold has been adjusted, press `Enter` and then accept/reject the image with the keyboard arrows.
(https://github.com/krishnapatil343/Krishna-Patil/assets/121950565/7edac453-ce24-4b5f-9341-5bcc66a192af)
Once all the riverline have been mapped, the output is available in two different formats (saved under */filepath/data/SITENAME*):
- `SITENAME_output.pkl`: contains a list with the shoreline coordinates, the exact timestamp at which the image was captured (UTC time), the geometric accuracy and the cloud cover of each individual image. This list can be manipulated with Python, a snippet of code to plot the results is provided in the example script.
- `SITENAME_output.geojson`: this output can be visualised in a GIS software (e.g., QGIS, ArcGIS).

The figure below shows how the satellite-derived riverline can be opened in a GIS software (QGIS) using the `.geojson` output. Note that the coordinates in the `.geojson` file are in the spatial reference system defined by the `output_epsg`.
<img width="1470" alt="Screenshot 2024-04-06 at 11 21 06" src="https://github.com/krishnapatil343/Krishna-Patil/assets/121950565/03d0b195-f378-4253-8e1e-70131ff895eb">
