# Introduction

This is an example of using OpenFOAM to analyise the aerodynamics of a Mazda FD RX7. 

Why the FD RX7? Well I managed to acquire one and have built a race car out of it, but also because the 
aero dynamics of the FD RX7 are somewhat understood. 

Here are some specs for the R1/R2 FD RX7

* Drag coefficient, Cd = 0.31
* Frontal area (A), sq meters/sw feet= 1.79/19.26
* Cd X A, sq meters/sq feet= 0.55/5.98
* Lift coefficient, front, Clf= 0.04
* Lift coefficient, rear, Clf= 0.01

Let's see what happens when we analyize it with OpenFOAM ... 
(BTW I should note, I have very little experiance with CFD and OpenFOAM, so there are probably lots of problems with my approach and results). 

## Caveats

 * The accuracy of the model hugely impacts the accuracy of the results, which may explain the differences between the specs and the results we get here
 * I have no idea what I'm doing with CFD software

## What you will need

You will need a computer or virtual machine Ubuntu 16.04LTS. OpenFOAM and ParaView are pretty CPU intensive, so ideally you would want to run them on actual hardware, but I started out doing this in a virtual machine, which worked fine, but was slow. 

## What is openfoam

OpenFOAM is an open source computational fluid dynamics software stack. 

http://www.openfoam.com/

## What is paraview

Paraview is a the viewer we'll use to examine the model and results. I think of it like a tool for viewing scientific models, it isn't specific to aerodynamics or our use case but instead is a toolkit to make it easy to view our results. 

http://www.paraview.org/

## Our process

 # Tweak our model in the various OpenFOAM text files
	# This might be tweaking our model, it might be changing other model parameters
 # Run openfoam for analytics
	# This will do an analysis over a specified timer period iteratively, each iteration producing some numbers about the model, ideally we need to wait for the model reach a stable state to really have an idea of how our model performed. 
 # View in paraview (/w paraFoam)

# Getting started

## Build/acquire a computer running Ubuntu 16.04 LTS 

	So you can use VirtualBox or VMWare to instal Ubuntu on. 

	http://releases.ubuntu.com/16.04/

	Download the 64 bit version, and install it, you will need approximately 12G of disk space and 2GB of ram.

## Make a copy of this repo

	Install git

	```
	sudo apt-get install git-core
	```
	
	Clone the repo (copy it)

	```
	git clone REPO
	```
	

## Follow the instructions here to install OpenFOAM:

	http://openfoam.org/download/4-0-ubuntu/

	Most notably:
	
```shell
sudo add-apt-repository http://download.openfoam.org/ubuntu
sudo apt-get update
sudo apt-get install openfoam4
```

You may also need to install paraview explicitly:

```shell
sudo apt-get install paraview
```

## Make sure you've got the OpenFOAM environment setup

```shell
source /opt/openfoam/etc/bashrc
```

## Let's run the analysis

In this repository directory, run:

```shell
./Allrun
```

This will take a while, figure 15minutes to an hour

## Looking at aerodynamics data

If you look at the log.simpleFoam you'll get some of the data we wanted to calculate:

```
forceCoeffs forceCoeffs1 write:
    Cm    = -0.0843553
    Cd    = 0.320536
    Cl    = -0.103392
    Cl(f) = -0.136051
    Cl(r) = 0.0326595
```

If you open up another terminal you can watch this log file as it is calculated using

```
	tail -f log.simpleFoam
```

## Let's view it in ParaView

In the repository directory run:

```shell
paraFoam
```

## Let's configure ParaView

First, let's remember that ParaView is a generic tool for viewing scientific data and results, so it requires a bit of work to to see our results. 

Let's take a look at the paraview UI to see what's going on:

1. Pipeline - The pipeline is shows the sequence of filters which will be run and shown on the screen 
2. Filter config - Lets you configure the filters 
2. View - Show's you what's going on

So to get results we need to stack a few filters together. 

So the way our pipeline will work is:

1. Model 
	Mesh data and aero data is passed to the next filter
2. Extract Block Filter
	We will use this to show only the mesh data we want to see
3. StreamFilter
	We will use the stream filter to show the stream lines
4. Tube filter
	We'll use this to make the streams a bit more visible

Each one of these filters passes some data to the next filter, so if the model doesn't pass the mesh's on to the extract block filter then we won't be able to see the mesh's, and if it doesn't pass on stream data we can't see that either. The filters work in a seqeuential fashion, and in the UI have a tree structure. 

### Configuring the model

### Configuring the extract block filter

### Configure the stream filter

### Configure the tube filter

# How to modify the analysis

## Airspeed 

Can be found here: 0.orig/include/initialConditions
```
	// Air speed in M/S
	flowVelocity         (20 0 0);
```

## Analysis runtime
Can be found here: system/controlDict

```
	startFrom       latestTime;
	startTime       0;
	stopAt          endTime;
	endTime         2000;
	deltaT          1;
	writeControl    timeStep;
	writeInterval   50;
```

## Details about the car model

Can be found here:  system/forceCoeffs

```
	/*
		CofR - Center of Rotation for calculating lift
		I set this to the midpoint of the axles at the specified CG height provided by mazda
	*/
	CofR        (1.219 0 0.413);  // Axle midpoint on ground, with Z = Center of gravity height
	pitchAxis   (0 1 0);
	magUInf     20;
	lRef        2.44;       // Wheelbase length (m)
	Aref        1.8;        // Frontal area (m^2)
```

## 

# Tricks and tips

## Viewing and editing meshes

I used either openscad or meshlab to modify or view the mesh. 

```shell
sudo yum install openscad meshlab
```

Then simply run either one

## Converting models

```
surfaceConvert rx7.stl rx7.obj
```

## Cleaning models

I used meshlab and netfabb cloud. 

## Calculating frontal area

If you make changes to the 3D model you need to recalculate the frontal area

To do so, open the file `rx7-frontal-area.scad` using openscad, and then click Design->Render and you'll get a 2D profile, save this to an SVG and measure the area.





	























