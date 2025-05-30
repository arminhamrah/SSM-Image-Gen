# VAE-with-SSMs

This repository hosts the implementations for (1) a Gaussian Variational Autoencoder (VAE) and (2) a VAE + State-Space Model (SSM) hybrid model—both for image generation and trained on the CelebA dataset. 

**VAE-CelebA.py** implements and trains a Variational Autoencoder using an encoder-decoder architecture on 64x64 pixel images from CelebA. The code is borrowed from an open-sourced PyTorch notebook (https://github.com/Jovana-Gentic/VAE_celeba/blob/main/pytorch-vae-celeba.ipynb). The code loads CelebA images using the provided partition CSV, preprocessing the images, defines the convolutional encoder and decoder (both modeling Gaussian distributions), trains the VAE, generates samples in batch sizes of 16, and plots the final training curves and output images.

Afterwards, there is a **VAE-losses.py** file which uses Pandas and Matplotlib to load the VAE_loss.csv file (with loss values at each step), and graphs all three loss functions—NELBO, REC, and KL—over the course of training. This script allowed us to overlay all three losses onto one graph rather than three seperate graphs, which was helpful for the class presentation visualizing the loss functions. For the SSM+VAE model, I generated three seperate graphs.

Lastly is **VAE-SSM.py**, which hosts the implementation code for the VAE-SSM hybrid model. 
VAE-SSM is a VAE hybrid in PyTorch that swaps out the usual convolutional neural networks for state-space (S4D) blocks (as introduced in the S4D paper). Both the encoder and decoder use positional embeddings plus S4D layers to turn 64×64 RGB faces into a compact latent code and back again. I trained on CelebA with a KL-annealing schedule and a warmup-exponential learning-rate decay, showing that structured state-space models can be a drop-in replacement for convolutional layers in image generation.

The CelebA dataset I used can be found here: https://www.kaggle.com/datasets/jessicali9530/celeba-dataset.

# Tech Stack

- **Language**: Python
- **Core Framework**: [PyTorch](https://pytorch.org/) (for model, training loop, optimizers)  
- **SSM Blocks**: `S4D` layers from the [state-spaces/s4](https://github.com/state-spaces/s4) repo  
- **Data & Transforms**: TorchVision (image I/O, `transforms.Resize` & `CenterCrop`) + `pandas` to parse CelebA splits  
- **Visualization**: `matplotlib` for loss curves & sample grids  
- **Progress Bar**: `tf.keras.utils.Progbar` (purely for colored console output)  
- **Checkpoints & Logging**: Python’s `pickle`/`torch.save` for results & model snapshots  
- **Hardware**: CUDA-enabled GPU 
- **Other Utilities**: `numpy`, `PIL`  

# Using this repository
0. [Optional] Create a virtual environment for this project (where you'll download all the necessary packages and dependencies) and set your Visual Studio Code environment up on a remote server which you can log into with an ssh key (training VAEs and SSMs are computationally expensive!).
1. Save the files in this repository in your associated IDE - in particular, VAE-CelebA.py and VAE-SSM.py, which host the code for training and running the two models.
2. Download the CelebA dataset from Kaggle onto your server or other coding environment: https://www.kaggle.com/datasets/jessicali9530/celeba-dataset
3. Update the dataset file paths in VAE-CelebA.py. By default, `path_to_files` for the dataset is `/cs/cs153/datasets/celeb_a_dataset/img_align_celeba/` and for the .csv's is `/cs/cs153/datasets/celeb_a_dataset/list_eval_partition.csv`. You should replace these within the create_filepaths() function with the actual filepaths to where you downloaded and extracted the dataset. E.g., if you put the dataset inside of a folder called 'celeba', you should update those lines to sometihng like this:
- filenames = pd.read_csv('celeba-dataset/list_eval_partition.csv') [currently line 74]
- path_to_files = 'celeba-dataset/img_align_celeba/' [currently line 80]

You should also ensure the CelebA dataset you download has those images in .jpeg or .jpg, which should be fine given that is the default of CelebA images.

4. In your terminal, after cd'ing into your folder with all of these files and the dataset, run "python VAE-CelebA.py" or "python VAE-SSM.py", depending on which model you want to train and run inference on.
5. For the SSM hybrid model, start off by cloning the official state space repo: https://github.com/state-spaces/s4
6. Move the file VAE-SSM.py to inside the cloned repo and run the file to train you model
7. Enjoy the results and share!
