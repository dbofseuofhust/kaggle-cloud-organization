Hello!

Below you can find a outline of how to reproduce my solution for the "Understanding Cloud Organization" competition.
If you run into any trouble with the setup/code or have any questions please contact me at davidcao1991@gmail.com

#ARCHIVE CONTENTS
kaggle_model.tgz          : original kaggle model upload - contains original code, additional training examples, corrected labels, etc
comp_etc                     : contains ancillary information for prediction - clustering of training/test examples
comp_mdl                     : model binaries used in generating solution
comp_preds                   : model predictions
train_code                  : code to rebuild models from scratch
predict_code                : code to generate predictions from model binaries

#HARDWARE: (The following specs were used to create the original solution)
Ubuntu 16.04 LTS (>=512 GB boot disk)
intel Xeon Gold 6130
1 x NVIDIA Tesla V100

#SOFTWARE (python packages are detailed separately in `requirements.txt`):
Python 3.7.3
CUDA 10.1
cuddn 7602
nvidia drivers v418.67

#DATA SETUP (assumes the [Kaggle API](https://github.com/Kaggle/kaggle-api) is installed)
# below are the shell commands used in each step, as run from the top level directory
mkdir -p input
cd input
kaggle competitions download -c understanding_cloud_organization

#Please make sure the unzipped train and test images are in the same folder named 'images'

#DATA PROCESSING
#This will convert the raw images to 384x576 and build .csv file for 5-fold info. The 5-fold info .csv is already included in ./kaggle-cloud-organization/files/

cd kaggle-cloud-organization
python preprocessing_images.py
python make_folds.py

#MODEL BUILD: There are 4 steps to reproduce the model. Shell command to run each step is below.

#1) Train seg1 model and predict.
bash ./kaggle-cloud-organization/run_seg1.sh

#2) Train seg2 models and predict.
bash ./kaggle-cloud-organization/run_seg2.sh

#3) Train cls model and predict.
bash ./kaggle-cloud-organization/run_cls.sh

#4) Ensemble the final predictions
cd output
mkdir -p b5-Unet-inception-FPN-b7-Unet-b7-FPN-b7-FPNPL
mkdir -p ensemble
cd ..
python ./kaggle-cloud-organization/mask-ensemble-5fold.py
python ./kaggle-cloud-organization/2-stage-ensemble-5fold.py

The final prediction file can be found as ./output/ensemble/test_5fold_tta3_cls.csv