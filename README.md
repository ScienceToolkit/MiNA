# MiNA - ImageJ Tools for Mitochondrial Morphology Research

## About the Project
MiNA (Mitochondrial Network Analysis) is a project aimed at making the analysis and characterization of mitochondrial network morphology more accurate, faster, and less subjective. It consists of a set of ImageJ macros that are used to measure meaningful morphological parameters from fluorescence micrographs of labelled mitochondria. The project began as a result of extensive work with adherent mammalian cell cultures in the laboratory of [Dr. Jeff Stuart](https://brocku.ca/mathematics-science/biology/directory/jeff-stuart/) at Brock University. Since then it has been rewritten as Python scripts adding support for 3D datasets and for using ridge detection (as suggested by one of the early reviewers of the paper we published about the macros - thank you for the suggestion!).

## Project Road Map
The links listed below provide a starting point for using, modifying, and discussing the project.

- :book: [**The Publication**](https://doi.org/10.1016/j.acthis.2017.03.001) - The publication regarding the original macros. Look at **Processing Pipeline and Usage** for more up to date and thorough information.
- :computer: [**Source Code**](https://github.com/StuartLab/MiNA/tree/master) - Fork the project and get hacking.
- :question: [**Issue Tracking**](https://github.com/StuartLab/MiNA/issues) - Submit bugs or feature requests here. Be sure to check the closed issues as well! We may have addressed a concern already.

## Processing Pipeline and Usage
<details>
 <summary>Determing the Area/Volume of Mitochondria</summary>
 </br>
 
 MiNA extracts morphological information from two simplifications of the image. One is a binary representation, which simply represents pixels as containing signal or being background. This is generated by automatic thresholding, a good overview of which available on the [Auto Threshold](https://imagej.net/Auto_Threshold) page. There are many thresholding methods available through ImageJ Ops. The methods included are listed below. If you find an issue arises using a specific thresholding method, please open an issue using the GitHub [issue tracker](https://github.com/StuartLab/MiNA/issues) (only a subset were tested). </br>
 
 Once the image has been binarized, the area or volume can be estimated by simply counting the number of signal positive pixels/voxels and multiplying by the area or volume of the pixel/voxel approximated as a rectangle or rectangular prism. In the binarized image, magenta represents the signal positive pixels, while the background pixels are black. This binarized copy is overlaid upon the original image after processing as an accuracy/artifact check measure. The area or volume of the image occupied by signal is returned as the mitochondrial footprint, the units of which will depend on how the image has been calibrated. You can check the image calibration under Image → Properties.
</details>

<details>
 <summary>Skeleton Analysis</summary>
 </br>
 
 A second simplification is made to the image for the purpose of estimating the lengths of mitochondrial structures and the degree of branching. The simplification is the generation of a morphological skeleton from which polylines can be extracted and analyzed (as is accomplished by the [Analyze Skeleton[1]](https://imagej.net/AnalyzeSkeleton) plugin). The skeleton itself can be generated in two ways. The first, iterative thinning, is the method used in the original macros and produces a skeleton by iteratively removing outer pixels until a one pixel wide structure remains. This is accomplished through the [Skeletonize (2D/3D)](https://imagej.net/Skeletonize3D) plugin's methods and has the added benefit of operating on 3D datasets as well as 2D. To use this method, mitochondria must be well resolved such that the individual mitochondria can be completely segmented from each other when the binary is generated. </br>
 
 Ridge detection has also been incorporated and generates a skeleton not from a binary but by using the fluorescence intensity itself. This is accomplished through the methods afforded by the [Ridge Detection[2][3]](https://imagej.net/Ridge_Detection) plugin. Ridge detection requires additional parameters, which are to be supplied at the prompt. It is advantageous to tune the parameters in the Ridge Detection plugin itself as it provides a preview mode. The parameters used are high contrast, low contrast, line width, and minimum length. Note that for Ridge Detection preview, images must be 8-bit. If your image is not 8-bit you must convert it using Image → Type. If using MiNA, there is no need to convert; it will be done automatically.</br>
 
 The information extracted from the morphological skeleton is the mean, median and standard deviation of the branch lengths for each independent feature and the number of branches in each network. No data is removed, so a feature that is simply a vertex, a rod without any branching points, or a complicated highly branched network will all be used when determining the length and branch count parameters. The information is summarized from the output of the Analyze Skeleton[1] plugin methods. Once processed, the skeleton is overlaid in green for assessing the faithfulness of the skeleton. Yellow and blue dots are also overlaid, representing the end points and junctions of the skeleton respectively.</br>
 
 It is <b>mandatory</b> that users ensure the morphological skeleton can be accurately generated for their system of interest. The analysis tool is typically not suitable for extremely clumped mitochondria, poorly resolved images, or images with low contrast. The overlay (or 3D render) is intended as a means to assess whether the model from which the parameters are generated will be meaningful or not.  
</details>

<details>
 <summary>Input Parameters</summary>
 <table>
  <tbody>
   <tr><td>Parameter</td><td>Description</td></tr>
   <tr><td>Pre-processor path (optional)</td><td>The path to an ImageJ script or macro to run before the analysis is run. This is typically used to run user defined preprocessing operations on the image before it is analyzed, such as expanding the histogram range, reducing noise by filtering, or deconvolving the image. It is optional</td></tr>
   <tr><td>Post-processor path (optional)</td><td>The path to an ImageJ compatible script or macro to be run when the analysis completes. This can be used to trigger saving a copy of the data, plotting the current data stored in the ResultsTable window, and much more. It is optional.</td></tr>
   <tr><td>Threshold method</td><td>The algorithm used to determine the threshold value. Note that the analysis expects a positive signal. The analysis will fail if the intensity is inverted.</td></tr>
   <tr><td>User comment</td><td>A user comment to store in the table. This supports key-value pairs, which is useful for storing condition information. For example, if I wanted a column "oxygen" and a column "glucose" to store the culture conditions, I could use the comment "oxygen=18,glucose=high" to add the value 18 to a column titled oxygen and the value high to another column titled glucose.</td></tr>
  </tbody>
 </table>
 
 <b>Ridge Detection</b>
 <table>
  <tbody>
   <tr><td>Use ridge detection (2D only)</td><td>Check if ridge detection is to be used. If it is unchecked, the analysis will proceed with iterative thinning to generate the morphological skeleton. Note that ridge detection is only available for 2D images currently.</td></tr>
   <tr><td>High contrast</td><td></td></tr>
   <tr><td></td><td>This defines the High Contrast value for the ridge detection plugin.</td></tr>
   <tr><td>Low contrast</td><td>This defines the Low Contrast value for the ridge detection plugin.</td></tr>
   <tr><td>Line width</td><td>The expected width of the line like mitochondrial features.</td></tr>
   <tr><td>Line length</td><td>The minimum line length to be included in the analysis. Setting this above 0 can aid in removing spurious small lines.</td></tr>
  </tbody>
 </table>
 
 <b>Median Filter</b></br>
 Smooths each pixel by replacing each pixel with the neighbourhood median.
 <table>
  <tbody>
   <tr><td>Radius</td><td>Size of the neighbourhood</td></tr>
 </tbody>
 </table>
 
 <b>Unsharp Mask</b></br>
 Sharpens and enhances edges by subtracting a blurred version of the image (the unsharp mask) from the original. The unsharp mask is created by Gaussian blurring the original image and then multiplying by the “Mask Weight” parameter.
 <table>
  <tbody>
   <tr><td>Radius (sigma)</td><td>Increase the Guassian blur radius sigma to increase contrast</td></tr>
   <tr><td>Mask weigth</td><td>increase value for additional edge enhancement</td></tr>
  </tbody>
 </table>
 
 <b>Enhance Local Contrast CLAHE</b></br>
 Enhances local contrast of the image.
 <table>
  <tbody>
   <tr><td>block size</td><td>the size of the local region around a pixel for which the histogram is equalized. This size should be larger than the size of features to be preserved.</td></tr>
   <tr><td>histogram bins</td><td>the number of histogram bins used for histogram equalization. The implementation internally works with byte resolution, so values larger than 256 are not meaningful. This value also limits the quantification of the output when processing 8bit gray or 24bit RGB images. The number of histogram bins should be smaller than the number of pixels in a block.</td></tr>
   <tr><td>max slope</td><td>limits the contrast stretch in the intensity transfer function. Very large values will let the histogram equalization do whatever it wants to do, that is result in maximal local contrast. The value 1 will result in the original image.</td></tr>
   <tr><td>mask</td><td>choose, from the currently opened images, one that should be used as a mask for the filter application.</td></tr>
  </tbody>
 </table>
</details>

<details>
 <summary>Analysis Output</summary>
 <table>
  <tbody>
   <tr> <td>Parameter</td> <td>Description</td> </tr>
   <tr> <td>image title</td> <td>The title of the image window that was processed.</td> </tr>
   <tr> <td>mitochondrial footprint	</td> <td>The area or volume of the image consumed by mitochondrial signal.</td> </tr>
   <tr> <td>branch length mean</td> <td>The mean length of all the lines used to represent the mitochondrial structures.</td> </tr>
   <tr> <td>branch length median	</td> <td>The median length of all the lines used to represent the mitochondrial structures.</td> </tr>
   <tr> <td>branch length stdevp	</td> <td>The standard deviation (population) of the length of all the lines used to represent the mitochondrial structures.</td> </tr>
   <tr> <td>summed branch lengths mean	</td> <td>The mean of the sum of the lengths of branches for each independent structure (as represented by the morphological/topological skeleton). This is the sum of all branch lengths divided by the number of independent skeletons.</td> </tr>
   <tr> <td>summed branch lengths median	</td> <td>The median of the sum of the lengths of branches for each independent structure (as represented by the   morphological/topological skeleton).</td> </tr>
   <tr> <td>summed branch lengths stdevp	</td> <td>The standard deviation of the sum of the lengths of branches for each independent structure (as represented by the     morphological/topological skeleton).</td> </tr>
   <tr> <td>network branches mean	</td> <td>The mean number of attached lines used to represent each structure.</td> </tr>
   <tr> <td>network branches median</td> <td>The median number of attached lines used to represent each structure.</td> </tr>
   <tr> <td>network branches stdevp</td> <td>The standard deviation (population) of the numper of connected lines used to represent each of the mitochondrial structures.
    </td> </tr>
   <tr> <td>user comment</td> <td>The comment supplied by the user. If key value pairs are provided, they will be put in an appropriate column.</td> </tr> 
  </tbody>
 </table>
</details>

<details>
 <summary>Running the Tool</summary>
 </br>
 
 <b>1.</b> Open an image. MiNA expects an 8-bit or 16-bit single channel image that can be 2D or 3D (with limited options). </br>
 
 <b>2.</b> Select a single cell using the rectangular ROI tool. You may make a copy of the cropped region if you wish. </br>
 
 <b>3.</b> Run the script by navigating to Plugins -> StuartLab -> MiNA Scripts -> MiNA Analyze Morphology. A user dialog will pop up. Fill in the parameters as desired and select OK to run the analysis. </br>
 
 <b>4.</b> An overlay as shown in an above figure will be generated for you to visually inspect the faithfulness of the analysis. The magenta region is the binarized signal used for calculating the area or volume. The green lines are the morphological skeleton. </br>
 
 <b>5.</b> To save a copy of the image with overlays, save the image as a PNG or flatten the image and save it in whatever format you wish. </br>
</details>

## Frequently Asked Questions (FAQ)
**1. Will MiNA work for my purposes/cells/images?**<br>
This is not a question we can definitively answer or one we will try to answer. In general, try it out and see if the skeleton and binary representations are faithful to the structures you see. You may want to do some preprocessing as well, but keep in mind how the preprocessing may be altering your images and the measurements (ex. blurring to reduce noise will often increase the measured footprint). In general, the tool is typically not suitable for extremely clumped mitochondria, poorly resolved images, or images with low contrast. The accuracy of the tool will be dependent on how well resolved the mitochondrial structures are. With fluorescence microscopy for certain cell lines where mitochondria severely clump together, it may not be possible to obtain the required resolution. It is the responsibility of the researcher to understand and validate MiNA for their purposes and decide if it is suitable.

**2. The parameters returned by the tool are not the same as in the paper. Where are the number of individuals and number of networks?**<br>
The parameters are slightly different. Some of the additional parameters had been requested by users and interested individuals. The list of all current parameters returned and how they are calculated is provided on the [wiki](https://imagej.net/MiNA_-_Mitochondrial_Network_Analysis#Processing_Pipeline_and_Usage) (take a look at the source code too!).

Two parameters, the number of individuals and number of networks, were removed. These parameters were deduced from the number of junction points in each skeleton (or "graph"). Since the only requirement for an object to be a network was a single junction point, when larger networks break down into smaller ones, the number of networks often increases as the objects often still have at least one junction point. This could easily misinterpreted as a more "fused" structure even though it has obviously become a more "fragmented" one because of how the jargon is interpreted. To avoid such confusion, we removed it.

**3. How can I familiarize myself with ImageJ/Fiji and image analysis first?**<br>
There are many resources in print and online to help with getting started with ImageJ and image analysis in general. Below is a list of some to get started with.

  * [ImageJ Tutorials Wiki](https://imagej.net/Category:Tutorials)
  * [Bankhead, Peter. (2014). Analyzing fluorescence microscopy images with ImageJ.](https://petebankhead.gitbooks.io/imagej-intro/content/)
  * [Vebjorn Ljosa and Anne E. Carpenter. 2009. **Introduction to the Quantitative Analysis of Two-Dimensional Fluorescence Microscopy Images for Cell-Based Screening.** PLOS Comput Biol.  doi: 10.1371/journal.pcbi.1000603.](https://dx.doi.org/10.1371%2Fjournal.pcbi.1000603)
  
 ## Points to keep in mind before using MiNA
 The output is likely to be inaccurate if:
 * Mitochondria are not independently resolved
 * Overlaps or 3D complexities are not trivial (usually applies to thicker cells)
 * Images and/or sample labelling is not good quality
 
 <details>
 <summary>More details on image suitability</summary>
 <table>
  <tbody>
    <tr>
      <td>
        Suitability
      </td>
      <td>
        Acquisition Resolution
      </td>
      <td>
        Contrast/Noise
      </td>
      <td>
        Cell Characteristics
      </td>
      <td>
        Preprocessing
      </td>
    </tr>
    <tr>
      <td>
        Possibly
      </td>
      <td>
        <ul>
          <li>
            Confocal/Spinning disk + deconvolution.
          </li>
          <li>
            Super-resolution (SR-SIM, STED, etc).
          </li>
          <li>
            Segmented electron microscopy stacks.
          </li>
        </ul>
      </td>
      <td>
        <ul>
          <li>
            Labelled with bright, membrane potential insensitive dye/marker.
          </li>
          <li>
            None or minimal non specific labelling.
          </li>
          <li>
            None or minimal non background flourescence.
          </li>
          <li>
            Low-noise.
          </li>
        </ul>
      </td>
      <td>
        <ul>
          <li>
            2D image of very thin adherent cells or 3D image of thicker cells.
          </li>
          <li>
            Simple/sparsely distributed mitochondrial structures.
          </li>
          <li>
            Primarily tubular or small punctate structures.
          </li>
        </ul>
      </td>
      <td>
        <ul>
          <li>
            Noise reduction (median filtering, non-linear local means, etc).
          </li>
          <li>
            Normalizing the brightness range in all images.
          </li>
          <li>
            Deconvolution.
          </li>
          <li>
            Structural filters such as Frangi/Vesselness filtering or LoG filtering in the same manner on all images.*
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td>
        Unlikely
      </td>
      <td>
        <ul>
          <li>
            Conventional fluorescence.
          </li>
          <li>
            Confocal/spinning disk with low NA objective.
          </li>
        </ul>
      </td>
      <td>
        <ul>
          <li>
            Weakly labelled mitochondria or labelled with membrane potential sensitive dye (such as TMRM).
          </li>
          <li>
            High background or noise.
          </li>
        </ul>
      </td>
      <td>
        <ul>
          <li>
            2D projection or slice of thick cells.
          </li>
          <li>
            Densely arranged mitochondrial structures.
          </li>
          <li>
            Tissues or 3D cultures
          </li>
        </ul>
      </td>
      <td>
        <ul>
          <li>
            Using different preprocessing in different sets of images.
          </li>
          <li>
            Using structural filters such as Frangi/Vesselness filtering and LoG filtering differently across different groups of images.
          </li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>
 </details>
 
 
 ## Installation
 **You should have [FIJI](https://fiji.sc/) installed.** </br>
 **1.** Download the contents of this repository as a zipped folder</br>
 **2.** Go to your FIJI installation and go to the "Fiji.app" folder. If you're using mac, right click on the FIJI icon and select "Show Package Contents".</br>
 **3.** Move the folder with the name "mina" located in "src" of this repository to jars>lib (create the folder lib if it does not already exist). </br>
 **4.** Move the file "MiNA_Analyze_Morphology.py" located at "src>scripts" to "scripts". </br>
 **5.** Move the folder "mina_icons" inside "images". </br>
 **6.** In the "build" folder of this repository you will see a jar file. Move it inside "plugins". </br> 
 **7.** Finally, start ImageJ and if you don't have the Biomedroup site installed then click on Help->Update...</br>
        Navigate to the update site manager (Manage update sites). Add the "Biomedgroup" site by checking the checkbox beside the site.</br>
        Close the site manager dialog (Close) and apply the changes (Apply changes)

 
