# LEAKAGEanalysis
Automated digital image processing pipeline for confocal microscopy 
Interactive guide for 2-photon image analysis

Image-J-Matlab: 2-photon imaging leakage analysis
The following program was built using the ImageJ-MATLAB plugin which allows information transfer between the two environments. You will be able open, view and save FIJI images in matlab. The matching/masking apps require the images to be saved as matrices in the workspace. This is achieved by the IJM Java function. The following documentation is aimed at familiarising the user with the data analysis steps. You will be able to use FIJI to extract your own dataset, or alternaltively, quickly run through an example set to familiarise yourself with the process. 


1. Setting working directory to app location, activating FIJI and image selection
For the script to run smoothly, and avoid the need to open apps manually make sure the current working directory is set to where all the apps and data are located. Next a dynamic java path is added. The path should lead to the matlab script folder that comes with the FIJI dowload. 
In FIJI select 4 images at the same position over for different time points 
e.g Image1=t0, image2=t1, image3=t2, image4=t4. 
Max intensity projecitons usually give a robust matrix with good vessel segmentation. But feel free to experiment and see what pre-processing method works best in terms of good vessel signal. Save pre-processed images to cd. A sample data set has been automatically loaded if you wish to skip the pre-processing step and go straight to app loading (section 2). 


cd '/Volumes/wikileaks/Documents/Thesis_data_docs'/'APPLICATION (1)'/ ;
addpath '/Applications/Fiji.app/scripts/' ;
ImageJ;
load selected_images.mat



The following comands will automatically assign whatever fiji window is selected to a workable matlab matrix. Select images in chronological order. 
Select fiji window that shows image corresponding to t0. 
Note: If you wish to use the image matrices that have been given, skip IJM steps. 
IJM.getDatasetAs('image1'); % first image is always baseline (t0)

Select fiji window displaying image corresponding to t1
IJM.getDatasetAs('image2'); % t1

Select fiji window displaying image corresponding to t2
IJM.getDatasetAs('image3'); % t2

Select fiji window displaying image corresponding to t3
IJM.getDatasetAs('image4'); % t3

This step creates a subplot of all image matrices. It allows the user to check if the intensity matrices are assigned correctly (respecting correct chronological order). 
select1 = subplot(2,2,1);
image(image1)

select2 = subplot(2,2,2);
image(image2)

select3 = subplot(2,2,3);
image(image3)

select4 = subplot(2,2,4);
image(image4)


2. Loading matrices for matching, masking and leakage quantification
Once images have been sucessfully asigned leakage analysis using the app GUIs can begin. Since the app is in  in your cd, it can be initialized here:
Load the images and follow the GUI sequence to measure vessel leakage for each image. 
run("app_loading_2p.mlapp");

Once you have applied to matching algorithm between fixed baseline and subseuqent images you can view all registrations here. If the matching is innaccurate feel free to move onto section 3 where you can perform manual matching via the image registration app. 
match1 = subplot(2,2,1); 
imshow(movingReg1)

match2 = subplot(2,2,2);
imshow(movingReg2)

match3 = subplot(2,2,3);
imshow(movingReg3)

3. Automatic registration preview and manual registration 
The image registration GUI uses the code below to match moving imeages (image2,3,4) to fixed basleine (image1). This is an intensity based registration wich accounts for possible local distortions in vessels. 
[optimizer, metric] = imregconfig('monomodal'); 

fixed = image1; % baseline DO NOT CHANGE
moving = image2; % select which image you wish to register
movingRegistered = imregister(moving, fixed, 'affine', optimizer, metric);

imshowpair(fixed, movingRegistered,'Scaling','joint')


Manual matching via Matlab registration app GUI
When automatic registration shows poor performance, matching can be done through the in-house registration app. Usually, MSER/SURF are pre-selected and show good performance. Use the sliders on the right-hand side to exclude innaccurate landmarks. Tick the nonrigid box in post-processing to correct for local warping.  Once statisfactory match is achieved, respect naming conventions 'movingReg[number]' . 
registrationEstimator(image2, image1);  

registrationEstimator(image3, image1);

registrationEstimator(image4, image1);

The code here extracts only the relevant matrix from the data structure created by the registration app during the export. This is essential for masking app compatibilitiy. 
movingReg1 = eval('movingReg1.RegisteredImage');
movingReg2 = eval('movingReg2.RegisteredImage');
movingReg3 = eval('movingReg3.RegisteredImage');

4. Plotting vessel leakage values
Once leakage qunatification has been completed, a table will be saved in the matlab workspace named 'means'. These are the saved avergae intensity values for the each image for both mask and no mask versions. To visualise the leakage over time it may be useful to create some plots. 
bar(Means)


