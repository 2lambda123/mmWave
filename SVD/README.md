# mmWave-SVD (Surface Velocity Detection Radar)
This repository contains the Batman Kit- 201 SVD Surface Velocity Detection mmWave Sensor SDK. The sample code below consists of instruction for using the mmWave lib. This mmWave-SVD Python Program will work with Surface Velocity Detection (SVD) based Batman BM201-SVD mmWave EVM Kit solution. This App works with Raspberry Pi 4 or Jetson Nano along with Batman BM201-SVD EVM Kit, and will report SVD data that include Doppler-Range Data, Point Cloud Data, Range Profile, and mmWave CPU Loading info; for application such as Water Stream Velocity Detection with range of 18.22 meters, and capable to distinguish water flow direction based on Doppler Data.

# Hardware:
    Batman kit-201 (ISK)
    Measure Range: 18.22 meters
    
# Installing

Library install for Python

    $sudo pip install mmWave
    $sudo pip3 install mmWave

Library update:

    $sudo pip install mmWave -U
    $sudo pip3 install mmWave -U

Examples:

    pyqtgraph_svd.py # show detected object, doppler
    
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
    
![MainMenu 1](https://github.com/bigheadG/imageDir/blob/master/svd.png)
    
## Header:

    class header:
        version = 0
        totalPackLen = 0
        platform = 0
        frameNumber = 0
        timeCpuCycles = 0
        numDetectedObj = 0
        numTLVs = 0
        subFrameIndex = 0
	

## Data Structure

### V1 Point Cloud Detected Object: Contains (X,Y,Z) coordinate and Doppler information of objects seen by the mmWave device
    
    V1 : [((float(x),float(y),float(z),float(Velocity)).....]
	tlvNDOCnt : Number of Detected Object Count
	x : X coordinate in meters
	y : Y coordinate in meters
	Z : Z coordinate in meters
	Velocity : Doppler velocity estimate in m/s. Positive velocity means target 
		is moving away from the sensor and Negative velocity means target 
		is moving towards the sensor.
	
	
### V2 Range Profile: Array of profile points at 0th Doppler (stationary objects). the points represent the sum of log2 magnitudes of received antennas.
    
	V2: (Range FFT) * 128
	size: 128 points  


### V6 Stats Information:

	V6: (interFrameProcessingTime ,   #interFrame processing time in usec
            transmitOutputTime ,        #Transmission time of output detection information in usec
            interFrameProcessingMargin, #interFrame processing margin in usec
            interChirpProcessingMargin, #interChirp processing margin in usec
            activerFrameCPULoad,        #CPU Load (%) during active frame duration
            interFrameCPULoad)          #CPU Load (%) during inter frame duration
	
		
### V7 Point Cloud Side Infomation:
    
     V7: [(snr,noise).....]
     
     snr: CFAR cell to side noise in dB expressed in 0.1 steps of dB
     noise: CFAR noise level of the side of the detected cell in dB expressed in 0.1 steps of dB
			
# function call:
	 
	getHeader()
	tlvRead()
	usage: (dck,v1,v2,v6,v7) = svd.tlvRead(False)
		    

# import lib

    from mmWave import surfaceVD

  ### raspberry pi 4 use ttyS0
    port = serial.Serial("/dev/ttyS0",baudrate = 921600, timeout = 0.5)

    
  ### Jetson Nano use ttyTHS1
  	port = serial.Serial("/dev/ttyTHS1",baudrate = 921600, timeout = 0.5)
	and please modify: 
	

## define

    svd = surfaceVD.SurfaceVD(port)

## get tlv Data

    (dck,v1,v2,v6,v7) = svd.tlvRead(False)
    dck: data check 1: available 0: data not ready

    v1 :Point Cloud
    v2 :Range Profile
    v6 :Stats Information
    v7 :Side Information of Detected Object

## Reference



