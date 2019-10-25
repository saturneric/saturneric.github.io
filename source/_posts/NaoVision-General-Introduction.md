---
title: NaoVision
date: 2019-10-09 23:22:44
tags:
---

# NaoVision Concise Document

## Project Summary

### Introduction

NaoVision is a dedicated NAO group of dedicated vision method solutions for NAO robots in the RoboCup competition. NaoVision was written in C++ and is designed to solve the problem that NAO robots in the Bhuman system under the original fixed threshold scheme for the color discrimination of the field is easily interfered by light and other factors, resulting in the actual game NAO robot for the objects in the field (especially football) The identification is not fast enough and accurate.

Correspondingly, for the judgment of the football field, NaoVision adopts the OTSU algorithm to realize the automatic adjustment of the image processing threshold, and is able to correctly identify and mark the field pixels in most cases without being affected by the light shadow.

Based on the color discrimination of the site, NaoVision creates a scan line in the picture. The boundary between the site and the non-site is determined by sampling and scanning the pixels where the scan line is located. The picture is then divided into part and non-site parts by connecting between the demarcation points.

In general, in order to reduce the computational overhead, NaoVision only calculates the picture pixels belonging to the site part. After determining the calculation range, NaoVison will create smaller scan lines within the calculation range for more detailed scanning and detection of objects within the calculation range.

Then, NaoVision limited the detection of circular objects in the field. The program will perform a uniform skip scan on the scan line. After detecting pixels that do not belong to the field within the calculation range, three non-collinear points are randomly selected around the pixel. Based on these three points, the scanning is performed in eight directions, and it is known that the pixels of the field are detected and returned. Then judge whether the 24 effective points are on a circle, and simultaneously calculate the center coordinates of the circle and its radius. After the circle is added to the candidate queue, the program will continue to scan the rest of the calculation area.

(To be continued)

### Main Component

Under NaoVision's solution (developed with Visual Studio 2019), there is

- **NAOFastTools** Core Toolset
- **NFTCommandLineTools** Common Command Line Tools
- **NFTDeamon** Daemon
- **NFTGUI** Debugging Interface
- **NFTTest** OpenCV Docking Toolset
- **UnitTest** Unit Test Project

### Brief Process Introduction

Problem Description: Due to various problems with the direct embedding scheme of NFT and BHuman, this project intends to adopt the independent process with BHuman.

In the actual game process, the **NAOFastTools Framework** is driven by the **NFTDeamon**, running in parallel with Nao qi and BHuman in the NAO robot system. **NFTDeamon** communicates with BHuman by sharing memory. Camera data is obtained from BHuman. After calculation, visual correction information is transmitted to BHuman. In the usual debugging process, you can use the **NFTCommandLineTools  ** or **NFTGUI Debug Interface** to establish a connection with the **NFTDeamon** to monitor and adjust the **NAOFastTools Framework** or **BHuman ** Operating status and related parameters.

(To be continued)

#### Introduction To Each Component

##### 1. NAOFastTools Core Toolset

The core framework for image processing and storage of the solution and the corresponding graphics processing module stored in the NAOFastTools Core Tools Group (hereinafter referred to as NFT) is the core project of the solution. The project follows the C++14 standard, and a small portion of the code contains C++14 features (the system with BHuman2018 has libstdc++.so that supports C++14). NFT eventually generates a static link library (NAOFastTools.lib and NAOFastTools.a) that corresponds to Windows and Linux. NFT works by linking with other programs. In addition to compiling in the VS environment, the NFT framework can also be compiled in a Linux environment, provided that the clang, make, and cmake tools are properly installed. The following is an extremely simple introduction to several of the most important common classes and functions in the NFT (including not limited to the following) (the actual use requires code understanding).

OperaMatrix is a proxy class that is a layer of proxy access to the Matrix class of the Eigen linear algebra library. In addition to providing proxy access services, it also provides a smart pointer service that automatically creates and releases Matrix objects based on reference counts. One or more OperaMatrix corresponds to a Matrix object. OperaMatrix objects provide a variety of constructors to meet the needs of users in most situations. The elements in OperaMatrix are stored in memory in a column-first manner, and the user can directly get a pointer to the first element of the matrix via the getData() interface. The number of rows and columns of the matrix corresponding to OperaMatrix can be obtained using getRows() and getCols().

The Image class is a storage vector for images that contains one or more OpreaMatrix objects, corresponding to multiple channels of the image. An OperaMatrix stores pixel data for a corresponding channel. Normally, the type of pixel data (PIXEL_TYPE) is defined in NFT.h. The number of channels in an image can be obtained using the getColumns() method, and a reference to a channel can be obtained directly using the [] operator. The height and width of the image are obtained using the getHeight() and getWidth() methods.

The ImageYUV422 class inherits from the Image class, which provides storage for the compressed color space YUV422. Need to note that it has one and only one channel. In a specific memory implementation, it compresses the pixel data of three channels into one channel for storage. If you need to get the Y, Cr, and Cb values of a pixel, you can call getY(int, int), getCr(int, int), getCb(int, int) to access it. Further, if it is necessary to modify the Y, Cr, and Cb values of a certain pixel in the picture, the corresponding setter is called and the coordinate parameters of the pixel are provided.

The ImageColored class inherits from the ImageYUV422 class and has one and only one channel. It mainly provides a single-channel image storage function and is a storage carrier for the output of the site color discrimination module. It is also a storage carrier for input data for many other vision modules. It disables all of the features of the ImageYUV422 parent class that have operations on the YUV color space.

The ImageSaver class is a storage service class for the Image class. It can store the data in the Image object correctly in a file on a Windows or Linux system (.nftd suffix). It can also build the corresponding Image object by reading the file in .nftd format.

(To be continued)

##### 2. NFTGUI Debugging Interface

The NFTGUI debugging interface is based on Qt5. It is a visual debugging interface for NAOFastTools. It can output intermediate results and internal related parameters for processing each graphics processing module.

(To be continued)

##### 3.NFTTest Opencv Docking Toolset

The NFTTest Opencv Docking Toolset is a bridge between the NaoVision framework and the OpenCV framework. It provides a conversion function between the main data structures between the two frameworks. In addition, the project is also used for the most basic debugging information output during the development process. .

(To be continued)

##### 4.UnitTest

Based on the Google Test test framework, the unit test code of the core framework in NAOFastTools is stored. This code will ensure that the core framework can run efficiently and accurately, and it is convenient to discover the loopholes in the framework in time.

(To be continued)