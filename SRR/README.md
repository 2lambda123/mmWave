# mmWave-SRR (Short Range Radar)
This repository contains the Batman Kit- 101 SRR mmWave Sensor SDK. 
The sample code below consists of instruction for using the mmWave lib.
This mmWave-SRR Python Program will work with Short Range Radar(SRR) based mmWave Batman Kit solution.
This App works with Raspberry pi 4 , Jetson Nano , linux/windows PC or Mac
The Short Range Radar (SRR) based Batman Kit-101 is capable of detecting obstacles' location, distance, approaching or moving-away 
(via Doppler Data) from the Radar in 20 meter or even 50 meter range pending object's size in terms of Radar Cross Section (RCS); 
programmer may then plot an obstacle-free zone to guide an Unmanned Ground Vehicle (UGV), a Blind Person, or a Drone to a safe zone.


# Installing

Library install for Python

    $sudo pip install mmWave
    $sudo pip3 install mmWave

Library update:

    $sudo pip install mmWave -U
    $sudo pip3 install mmWave -U

Examples:

    pyqtgraph_srr_xy.py # show detected object, doppler and Parking Bins
    
If Run demo program can not find any Raw data output:
      Please set UART to R/W mode: 
      
      pi 3
      $ls -l /dev/ttyS0
      $sudo chmod +777 /dev/ttyS0
      
      pi 2 
      $ls -l /dev/ttyS0
      $sudo chmod +777 /dev/ttyAMA0
      
      jetson
      $ls -l /dev/ttyTHS1
      $sudo chmod +777 /dev/ttyTHS1

https://user-images.githubusercontent.com/2010446/118252784-b7d59f00-b4db-11eb-8442-2214cd5fcfd3.mov
    
## Header:

    class header:
	version = "0.0"
	totalPackLen =0
	platform = ""
	frameNumber = 0
	timeCpuCycles = 0
	numDetectedObj = 0
	numTLVs = 0
	tvlHeaderLen = 8
	subFrameNumber = 0

## Data Structure

#V1 Detected Object: Contains range, angle, (X,Y) coordinate and Doppler information of objects seen by the mmWave device
    
	V1 : (tlvNDOCnt,float(x),float(y),float(doppler_v1),float(range_v1),float(peakValue))
	tlvNDOCnt : Number of Detected Object Count
	x : X coordinate 
	y : Y coordinate	
	doppler_v1 : v1 Doppler
	range_v1 :   v1 Range
	peakValue : 
	
#V2 Cluster Object:
    
	V2: (tlvNDOCnt,float(xc),float(yc),float(xcSize),float(ycSize))
	tlvNDOCnt : Number of Detected Object Count
	xc : X coordinate 
	yc : Y coordinate 
	xcSize : X size
	ycSize : Y size

#V3 TRACK Data
    
	V3: (tlvNDOCnt,float(xt),float(yt),float(xtSize),float(ytSize),float(vxt),float(vyt),float(tRange),float(tDoppler))
	tlvNDOCnt : Number of Detected Object Count
	xt :  X coordinate 
	yt :  Y coordinate 
	vxt : X Velocity for Doppler
	vyt : Y Velocity for Doppler
	xtSize : X size
	ytSize : Y size
	tRange : Range  #sqrt(xt^2+yt^2)
	tDoppler = Doppler
		
#V4 Parking AssistBin Array
    
	[jb_val,...] : float 
			
# function call:
	 
	getHeader()
	tlvRead()
	usage: (dck,v1,v2,v3,v4) = srr.tlvRead(False)
		    

# import lib

    from mmWave import srradar

  ### raspberry pi 4 use ttyS0
    port = serial.Serial("/dev/ttyS0",baudrate = 921600, timeout = 0.5)

    
  ### Jetson Nano use ttyTHS1
  	port = serial.Serial("/dev/ttyTHS1",baudrate = 921600, timeout = 0.5)
	and please modify: 
	
	#import RPi.GPIO as GPIO
	import Jetson.GPIO as GPIO

## define

    srr = srradar.SRR(port)

## get tlv Data

    (dck,v1,v2,v3,v4) = srr.tlvRead(False)
    dck: data check 1: available 0: data not ready

    v1 :objectDet
    v2 :ClusterData
    v3 :TRACK Data
    v4 :PARKing Assist Data

## Reference

1. LabGuide: https://github.com/bigheadG/mmWaveDocs/blob/master/AutoSrr_usersguide.pdf

             (Alert: if DATA STARUCTURE could not be found in PDF please see above Data Structure of README.md instead)

2. KeyDataProtocol: https://github.com/bigheadG/mmWaveDocs/blob/master/V4_SrrKeyDataProtocol_v04_03_pdf.pdf

