# FTIR-python-tools

### What is it?
This is a collection of scripts for the purpose of processing and extracting information from fourier transform infrared spectra. 

The project was created by Lars Yunker at the University of Victoria, Victoria, B.C., Canada

### Requirements and Installation:
The scripts were written for Python 2.7.

##### Getting started
* Download the entire repository to a folder and load scripts as needed. 
* Inexperienced python users can edit the input parameters in the files directly. 
* Experienced python users can import many of the classes and scripts directly (e.g. in iPython). Not all scripts are completely callable yet (work in progres). 

Additional instructional videos for using the other scripts are in development.

### Errors
If you encounter an error, please submit an Issue in Github with as much information as possible
Things that are helpful to include:
* the exact parameters you were using
* any files that you were supplying to the script
* the python error output

### Currently available tools:
##### ScriptTime, XLSX classes
These are taken directly from [mass-spec-python-tools](https://github.com/larsyunker/mass-spec-python-tools "mass-spec-python-tools"). If you encounter any errors while using these classes, download the latest version from that repository. 

#### _apodizer
This class generates and stores an apodization (window) function defined by the supplied method name. It can then repeatedly apodize a supplied list of values with that function. The class was created out of convenience so that a function did not have to be generated every time apodization was required. 

#### FTIR
This method takes an interferogram and processes it into an infrared spectrum. The processing method is currently as follows:

1. The interferogram is zeroed (centered around zero)

2. The interferogram is apodized about the zero-path-difference (ZPD)

  * The script was written to accomodate a single-sided interferogram, and applies a symmetric apodization to both sides of the ZPD (e.g. the apodization applied to the right side of the interferogram is scaled from the zpd to the end of the list, and the left side has the same apodization values applied).

3. A ramp is applied to the centerburst to avoid "wiggles"
  * The ramp is 0 at the 0th index and ramps linearly to a value of 1 at the index 2 times that of the zpd
  * After the index of twice the zpd, the factor remains at 1 for the rest of the interferogram.

4. The interferogram is zero-padded to the length specified by the user (default length of 2^16)

5. The interferogram is rotated (see the rotate method below)

6. A fast fourier transform is applied to the interferogram

7. The result of the FFT is phase corrected using the Mertz method:

  1. The n number of points around the centerburst are extracted (the number of points can be specified by the user)

  2. The centerburst is apodized

  3. The list is rotated

  4. A FFT is applied to the rotated list

  5. The power spectrum is extracted from the FFT result

  * This is the arctangent of the ratio of the imaginary part divided by the real part of the FFT result

  6. The power spectrum is interpolated (linearly) to match the size of the FFT from point #6 above

  7. The FFT result (from point #6) is multiplied by e^(-i*powerspectrum), and the real part of this is the final IR spectrum.

8. The first half of the symmetric IR spectrum is returned by the function.

##### A note on the mathmatics
The author of this script does not have intimate knowledge of the mathmatical processes involved in FTIR spectroscopy, and would greatly appreciate any input from those more knowledgable. If you have comments, suggestions, or other input into this project, please contact the author. 

#### FTIR_run (non-functional; in development)
This method is designed to extract a series of FTIR interferograms from an XML file, process them, and have the ability to integrate over the scans (provided they are time-resolved). Some things to note about this file:
  * The XML files were generated from Bruker's OPUS software, so XML files generated by other software may have different syntax that this script will not know how to handle. 
  * The output of the integration does not yet match that of OPUS. The reason for this discrepancy is under investigation. 
  * The XML file is expected to contain a reference, to which all the IR spectra are normalized

#### kinetics_from_trace
This method attempts to fit an order profile to supplied x and y list data. It can be supplied with either an *.xlsx file (which contains x and y columns in the specified sheet) or an x and y list. The script will try to fit a decay of the specified order to the supplied data. Additional functionality: 
  * Generation and output of publication-quality figures of the fitted data. 
  * If supplied with an xlsx file as input, the simulated data and statistical output can be written to that file as output

#### rotate
This method "rotates" an interferogram about the ZPD. This makes it so that the side of the pulse to the right of the ZPD is at the beginning of the list, and the side of the pulse to the left of the ZPD is at the end of the list. 

### License
These tools are not yet licensed. 