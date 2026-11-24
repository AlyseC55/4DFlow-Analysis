4D Flow MRI Vessel Velocity Viewer

An interactive Jupyter tool for reviewing 4D flow MRI scans. Pick a patient's scans, draw a line across a vessel, and get a velocity-over-time curve for that whole vessel.

What it does
- Lists 4D flow series found in a folder of DICOM studies, with search by patient name or MRN.
- Shows magnitude, velocity (Vx, Vy, Vz) and MRA images slice by slice and frame by frame, in axial, coronal or sagittal views.
- Builds an MRA (time-averaged, magnitude- and velocity-weighted flow map) normalized across the whole volume, so one threshold  applies to every slice.
- Segments a vessel from a line you draw: it grows the connected region above the MRA threshold in 3D and uses only the slices that region spans.
- Plots the mean Vx, Vy and Vz over the segmented vessel for every time frame, plus an along-vessel curve that projects each voxel's velocity onto its own mean flow direction.
- Exports the plotted curves to CSV or Excel.

Requirements
Python 3 with Jupyter (JupyterLab or Notebook).
Packages: pydicom, pandas, numpy, matplotlib, ipywidgets, ipycanvas, tqdm.
Optional: PyQt5 for the folder dialog (the notebook falls back to tkinter without it), and openpyxl for Excel export.

pip install pydicom pandas numpy matplotlib ipywidgets 
ipycanvas tqdm
pip install PyQt5 openpyxl

Usage
- Open the notebook and run the cells in order.
- In the scan-folder cell, choose the main folder that contains one folder per patient. Only series whose description contains "4D" are listed.
- Run the viewer cell, search for a patient, tick the magnitude and phase series of the same acquisition, and click Run.
- Check Venc (cm/s) against the acquisition protocol. If the series does not record a venc, the viewer defaults to 150 cm/s.
- In the MRA tab, raise the MRA threshold until the vessel stands out, then click and drag a line across the vessel.
- Read the curves, and use the export buttons to save them.

A full step-by-step procedure is in the project's SOP.

Data and privacy

This repository contains no patient data, and none should ever be committed. The scan folder is chosen at run time and is read locally. Nothing is uploaded.

The viewer displays patient names and MRNs, and running it can create files that contain them. Never commit:

DICOM files (.dcm).
Scan lists (CSV or Excel files).
Notebooks that still contain saved outputs, because the patient list is stored in them.
Screenshots that show names, MRNs or study dates.

How the curves are calculated
- Phase pixel values are converted to velocity in cm/s using the venc, assuming a 12-bit range centered on 2048. Frame 0 is an empty placeholder and is skipped.
- MRA = the square root of the time average of (magnitude squared times Vx² + Vy² + Vz²), scaled from 0 to 1 over the volume.
- The vessel is every voxel at or above the threshold that is connected in 3D (including diagonal neighbors) to the voxels the line crosses.
- The Vx, Vy and Vz curves are the means of each component over the vessel at each time frame.
- The along-vessel curve projects each voxel's velocity onto its own time-averaged flow direction and averages across voxels, weighted by mean speed. This avoids cancellation in curved vessels.

Limitations
- Curves are velocity in cm/s, not volumetric flow rate.
- There is no background phase offset correction.
- Phase encoding (12-bit, centered on 2048) and a default venc of 150 cm/s are assumed. Unlabeled velocity series are treated as Vx, Vy, Vz in order.
- The segmentation depends on the MRA threshold, and touching vessels can merge.
- The along-vessel curve does not show reversed flow as a negative value.
- Coronal and sagittal views assume the scan was acquired as axial slices.
- Images are drawn pixel for pixel and do not use the DICOM pixel spacing.