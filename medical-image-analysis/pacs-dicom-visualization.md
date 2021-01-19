# PACS/DICOM/Visualization

PACS\(Picture Archiving and Communication System\)

DICOM\(Digital Image and COmmunications in Medicine\)

* 영상 정보 
* DICOM\(.dcm\) Analyze\(.hdr/img\) .mhd/raw

Medical Image Format

* Dimensions \(256, 256, 128\)
* Voxel spacing \( 0.5, 0.5, 1.0\)
* Origin\(0, 0, 0\)

-&gt; voxel를 맞추고 진행해야 전처리가 잘 됨. 

registration도 voxel과 origin를 맞춰야 정합이 가능하다. 

Medical Image Visualization 

* ITK-SNAP
* 3D Slicer
* ImageJ 
* Qupath

영상 열어보는 툴 

-&gt; segmentation을 툴에서 하는 편임 volume 확인 가능

#### 가시광선

Endoscopy

내시경 영상 - light source, sensor 

신호를 센서로 받아서 처리 

Microscopy

현미경 - 배율을 바꿔가면서 영상 확인 

#### 방사선

X-ray

이미징을 하려면 light source가 있어야 함 

x-tube 

Computed Tomography

3D영상 

여러 가지 2D 영상을 3D image로 

2d로부터 3d로 reconstruction 한다 -&gt; 적분을 이용해서 영상을 더해 주면 3차원 영상이 복원된다. 

Positron Emission Tomography



Magnetic Resonance Imaging 

공명현상

-&gt; 몸 안에는 수소원자들이 많음 \(물\) 수소 원자핵들이 랜덤한 방향을 가지는데 강한 magnetic을 주면 한 방향으로 됨 RF Pluse를 주면 원자들이 주는 속도만큼 돈다. 

### Image Acquisition

 





