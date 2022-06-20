# Human trajectory forecasting
This repository contains the code for the human trajectory forecasting method developed as part of the following Master's thesis https://comserv.cs.ut.ee/ati_thesis/datasheet.php?id=75142&year=2022

The main repository for the Master's thesis resides in https://github.com/karlriis/trajectory-forecasting. Some additional useful files and notebooks can be found there.

## Setup
The method was developed and tested with Python 3.9 and the libraries listed in `requirements.txt`.
The required libraries can be easily installed via `pip install -r requirements.txt`.

## Our method
The new trajectory forecasting method resides in `generative_model.py`.
The main function for using the method is `predict()`. Its input parameters are shown in the table below (non-compulsory parameters with default values are marked with \*).

| Parameter name  | Parameter value | Meaning |
| ------------- | ------------- | ------------- |
| sample_x  | List of floats, e.g. [1.0, 1.8, 2.3, ...]  | The x-coordinates of an observed historical trajectory |
| sample_y  | List of floats, e.g. [1.0, 1.8, 2.3, ...]  | The y-coordinates of an observed historical trajectory |
| params  | Dictionary (possible values brought out separately below)  | The parameters for our method which manipulate the inner workings of the method |
| trajectory_length*  | Integer (default 5)  | Marks the length of the predicted trajectories |
| clustering_method*  | String ('KMeans' or 'KMedoids', default 'Kmeans') | The clustering method used for choosing representative predictions |
| smoothing*  | Boolean (default True) | Applies smoothing to representative predictions to reduce sudden turns |

The following shows a possible value for the `params` argument:
```
params = {
    'NOISE': 0.05, 
    'NO_OF_TRAJECTORIES': 500, 
    'CONST_VEL_MODEL_PROB': 0.5, 
    'STOP_PROB': 0.025, 
    'DISCOUNT_AVG_PROB': 1.0, 
    'DISCOUNT_LOWER_BOUND': 0.15, 
    'VELOCITY_CHANGE_PROB': 0.1,
    'VELOCITY_CHANGE_NOISE': 0.1, 
    'ANGLE_CHANGE_PROB': 0.2, 
    'ANGLE_CHANGE_NOISE': 2, 
    'GROUP_PERCENTAGES': [0.1, 0.5, 0.75, 1.0], 
    'GROUP_CLUSTER_COUNT': [1, 9, 6, 4]
}
```

The method outputs multiple trajectory predictions with probabilities as follows:
```
[
  [[0.4, 0.6, 0.7, ...], [1.3, 1.4, 1.6, ...], ...], # The x-coordinate values for each future trajectory 
  [[0.9, 0.8, 0.5, ...], [1.1, 0.8, 0.4, ...], ...], # The y-coordinate values for each future trajectory
  [0.2, 0.11, 0.07, ...]                             # The probability/weight of each future trajectory
]
```
## Useful notebooks
An experiment comparing the performance of the new method to Trajectron++ and the constant velocity model is in the notebook `results_comparison.ipynb`.

## Visualization
A visualization script `visualize.py` is included for illustrating the workflow 
of the new method in real time on existing datasets. It opens a PyQt UI which allows to 
visualize the movement of humans in the ETH dataset and the predictions of the new forecasting method.

![visualization demo](media/visualization_demo.gif)

The script is based on the visualization script provided in the OpenTraj repository https://github.com/crowdbotp/OpenTraj. 
The datasets with videos are sourced from there as well. The script currently supports the visualization of the sets of data in the ETH dataset (eth and hotel).
The script can be enhanced by including other datasets with videos from OpenTraj to the `./raw_data` folder and by editing the end of `visualization.py` to accomodate the new sets similarly to ETH.

The script has three non-compulsory parameters:
- `--data-root` specifies the location of the OpenTraj datasets (by default in the `./raw_data` folder)
- `--dataset` specifies which dataset to visualize, currently the only options are the `eth` and `hotel` (default is `eth` )
- `--model-params` provides a path to a .json file containing the parameters for our method (the `params` dictionary shown above). By default a simple configuration is used which produces 4 predictions.

and one boolean flag
- `--record` when the flag is provided then the visualization played by the script is recorded to a separate file named `output.avi`. This can be useful as the visualization UI can get quite slow when a lot of people are moving around in the scene, the recording is created in a fixed framerate which allows for smooth playback.

An example of calling the script with all options:
```
$ python visualize.py --data-root './raw_data' --dataset 'eth' --model-params './example_params.json' --record
```

Pre-rendered visualization videos of the eth and hotel datasets are stored under `media`.
